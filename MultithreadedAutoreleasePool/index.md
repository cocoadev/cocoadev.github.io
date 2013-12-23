---
layout: page
title: MultithreadedAutoreleasePool
---

Hey, this here is an NSAutoreleasePool subclass which sends objects to another thread to be released. Normal warnings about multithreaded code apply. I just found this useful for UI response in one part of my program, so here you are, use at your own risk. I am lazy and am just using volatile integers for my FIFO implementation. They work fine on my uniprocessor system, but you really need to change them to true atomic variables in shipping code.

Oh yeah, FastAutoreleasePool is most likely a misnomer, but it is shorter than MultithreadedAutoreleasePool, and I beleive MT is taken as a prefix by a very cool coreaudio library writer. 

PS. My pools dealloc method is never called, so I put cleanup in release instead. Since autorelease pools cannot be retained anyways, release is more or less the same as dealloc, but I would like to know why the behavior is as such. I am not even sure if autorelease pools are supposed to be subclassed, but the class seems to work fine for me.

Oh yeah, BSD license.

--JeremyJurksztowicz

Header file.
    
#import <Cocoa/Cocoa.h>

struct FastAutoreleasePoolCore;

@interface FastAutoreleasePool : NSAutoreleasePool
{
	struct FastAutoreleasePoolCore * core;
}
@end


Implementation file.
    
#import "FastAutoreleasePool.h"

#include <mach/mach.h>
#include <mach/mach_error.h>
#include <mach/mach_time.h>

#include <vector>
#include <stdexcept>
#include <algorithm>
#include <memory>
#include <iostream>

using namespace std;

namespace {
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
class Semaphore
{
public:
	Semaphore ( )
	{
		kern_return_t err = semaphore_create(
			mach_task_self(), &_sem, SYNC_POLICY_FIFO, 0);
			
		// Failed to create mach semaphore, use the mach error reporting function.
		// to translate return codes into something more useful.
		if(err) 
		{
			char * errMessage = "Unable to create mach semaphore.";
			mach_error(errMessage, err);
			throw std::runtime_error(errMessage);
		}
	}
	
	~Semaphore ( )
	{
		semaphore_destroy(mach_task_self(), _sem);
	}
	
	void signal ( )
	{
		semaphore_signal(_sem);
	}
	
	void wait (unsigned secs, unsigned nanos)
	{
		if(secs || nanos) {
			mach_timespec_t timeout = {secs, nanos};
			semaphore_timedwait(_sem, timeout);
		}
	}

private:
	semaphore_t _sem;
};
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
template<typename T> 
class Fifo
{
public:
	Fifo (unsigned int buffSz): 
		_readIndex (0), 
		_writeIndex(0), 
		_buffer    (buffSz) { }

	inline bool get (T& t) 
	{
		if(_readIndex == _writeIndex) 
			return false;
			
		t = _buffer[_readIndex];
		_buffer[_readIndex] = T(); // Reset value.
		
		if ((_readIndex + 1) >= _buffer.size()) _readIndex = 0;
		else _readIndex = _readIndex + 1;
		return true;
	}
	
	inline bool canGet ( ) const
	{
		if(_readIndex == _writeIndex) 
			 return false;
		else return true;
	}
	
	inline bool put (T const& data) 
	{
		unsigned int newIndex;
		if((_writeIndex + 1) >= _buffer.size()) 
			 newIndex = 0;
		else newIndex = _writeIndex + 1;

		if(newIndex == _readIndex) return false;
		_buffer[_writeIndex] = data;

		_writeIndex = newIndex;
		return true;
	}

	inline bool canPut ( ) const
	{
		unsigned int newIndex;
		if((_writeIndex + 1) >= _buffer.size()) newIndex = 0;
		else newIndex = _writeIndex + 1;

		if(newIndex == _readIndex) 
			 return false;
		else return true;
	}

	unsigned size ( ) const { return _buffer.size(); }

private:
	// NOTE: Not actually atomic, replace with either a platform dependant
	// atomic operation, or wrap accesses in a mutex.
	volatile unsigned _readIndex;
	volatile unsigned _writeIndex;
	vector<T> _buffer;
};
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
void ReleaseObject (id obj)
{
	[obj release];
}

} // END namespace
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
@interface ThreadRunner : NSObject
{ }
- (void) startThread:(id)core;
@end

struct FastAutoreleasePoolCore
{
	FastAutoreleasePoolCore (unsigned fifoSize): 
		_fifo(fifoSize) 
	{ }
	
	void start ( )
	{
		ThreadRunner * tr = [[ThreadRunner alloc] init];
		[NSThread 
			detachNewThreadSelector:@selector(startThread:)
			toTarget:tr
			withObject:[NSValue valueWithPointer:reinterpret_cast<void*>(this)]];
		[tr release];
	}
	
	void autorelease (id obj)
	{
		_fifo.put(obj);
		_sem.signal();
	}
	
	void threadLoop ( )
	{
		static const unsigned waitSecs = 0, waitNanos = 10000;
		vector<id> storage;
		
		NSAutoreleasePool * arp = [[NSAutoreleasePool alloc] init];
		while(true)
		{
			id obj;
			if(_fifo.get(obj))
			{
				if(nil == obj)
				{
					for_each(storage.begin(), storage.end(), ReleaseObject);
					storage.clear();
					break;
				}
					
				else storage.push_back(obj);
			}
			else _sem.wait(waitSecs, waitNanos);
		}
		[arp release];
		delete this;
	}
	
	void exitThread ( )
	{
		_fifo.put(nil);
	}

private:
	FastAutoreleasePool *	_client;
	Fifo<id>		_fifo;
	Semaphore	_sem;
};

@implementation ThreadRunner
- (void) startThread:(NSValue*)parm
{
	FastAutoreleasePoolCore * core = 
		reinterpret_cast<FastAutoreleasePoolCore*>([parm pointerValue]);
	assert(core);
		
	core->threadLoop();
}
@end
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////
@implementation FastAutoreleasePool

- (id) init
{
	if(self = [super init])
	{
		try {
			// Fiddle with this, or make it a parameter to init.
			static const unsigned defaultFifoSize = 32;

			auto_ptr<FastAutoreleasePool> temp(new FastAutoreleasePoolCore(defaultFifoSize));
			temp->start();
			core = temp.release();
			return self;
		}
		catch (std::exception const& err)
		{
			cerr << "Error creating FastAutoreleasePool : " << err.what() << endl;
		}
		catch (...)
		{
			cerr << "Unknown error creating FastAutoreleasePool." << endl;
		}
	}
	
	[self release];
	return nil;
}

- (void) release
{
	if(core) core->exitThread();
	[super release];
}

- (void) dealloc
{
	[super dealloc];
}

- (void) addObject:(id)obj
{
	core->autorelease(obj);
}

@end

