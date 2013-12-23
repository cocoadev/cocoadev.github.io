---
layout: page
title: NSDataCppStream
---

Hey,
I use Objective-C++ a lot, and some of my C++ classes support archiving using C++ iostreams. Now, I am not a big fan of iostreams, but am a fan of reuse, so I needed a way to make the archivable classes usable with undo. Unfourtunately, the Cocoa undo system needs to retain a reference to it's selector's arguments, and that is not quite possible with C++ stream objects. At first I used temporary files, and passed an NSString of the path to the undo facility, but this quickly became convoluted and difficult to maintain. So I wrote this custom object to stream to and from an NSMutableData object. I don't exactly know how to test iostreams, as they behave very oddly, and I am not an well versed in them. But I did do some tests by replacing the stream object with various 'tested and robust' stream classes (mostly fstream) and they behaved equally retarded. This code is preliminary, and I would like to add read-only (istream) version that accepts the non-mutable NSData class.
-JeremyJurksztowicz

IMPORTANT> after streaming all of your data into an NSDataStream remember to call flush() or the data object will be incomplete.

    
#include <string>
#include <streambuf>
#include <ostream>
#include <stdexcept>

#import <Foundation/Foundation.h>
////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////
// This class prevents serious compiler (linker, assembler...?) errors.
//
class NSDataWrapper
{
	NSMutableData * _data;
public:
        NSDataWrapper(NSMutableData * data): _data([data retain]) { }
       ~NSDataWrapper( ) { [_data release]; }
   
	void read (unsigned char * buf, unsigned pos, unsigned amt)
	{
		[_data getBytes:reinterpret_cast<void*>(buf) range:NSMakeRange(pos, amt)];
	}
	
	void write (unsigned char * buf, unsigned amt) 
	{
		[_data appendBytes:reinterpret_cast<void*>(buf) length:amt];
	}
	
	unsigned byteCount ( ) const
	{
		return [_data length];
	}
};
////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////
template <class charT, class traits = std::char_traits<charT> >
class NSDataStreamBuffer : public std::basic_streambuf<charT, traits>
{
	typedef std::basic_streambuf<charT, traits> sbuftype;
	typedef typename sbuftype::int_type         int_type;
	typedef charT                               char_type;

public:
	explicit NSDataStreamBuffer(NSMutableData * data, std::streamsize bufsize = 512):
		_nsdata		(data), 
		_inbuf		(NULL), 
		_outbuf		(NULL), 
		_ownsBuffers(false), 
		_readPos	(0),
		_bufsize	(bufsize),
		_remained	(0)
	{ }

	~NSDataStreamBuffer()
	{
		_flush();

		if(_ownsBuffers)
		{
			delete [] _inbuf;
			delete [] _outbuf;
		}
	}

protected:
	sbuftype * setbuf(char_type *s, std::streamsize n)
	{
		if(!this->gptr())
		{
			if(_ownsBuffers)
			{
				delete _inbuf;
				_inbuf = 0;
				delete _outbuf;
				_outbuf = 0;
			}
			setg(s, s + n, s + n);
			setp(s, s + n);
			_inbuf	 = s;
			_outbuf	 = s;
			_bufsize = n;
			_ownsBuffers = false;
		}
		return this;
	}

	void _flush()
	{
		_nsdata.write(reinterpret_cast<unsigned char*>(_outbuf), 
			(this->pptr() - _outbuf) * sizeof(char_type));
	}

	int_type overflow(int_type c = traits::eof())
	{
		if(!this->pptr())
		{
			_outbuf = new char_type[_bufsize];
			_ownsBuffers = true;
		}
		else _flush();
		
		setp(_outbuf, _outbuf + _bufsize);
		if(c != traits::eof())
			sputc(traits::to_char_type(c));
			
		return 0;
	}

	int sync()
	{
		_flush();
		setp(_outbuf, _outbuf + _bufsize);
		return 0;
	}

	int_type underflow()
	{
		if(!this->gptr())
		{
			_inbuf = new char_type[_bufsize];
			_ownsBuffers = true;
		}

		if(_remained != 0)
			_inbuf[0] = _remainedchar;

		unsigned bufLeft = _bufsize * sizeof(char_type) - _remained;
		unsigned datLeft = _nsdata.byteCount() - _readPos;
		unsigned readn = MIN(bufLeft, datLeft);
		if(readn == 0)
			return traits::eof();
		
		_nsdata.read(reinterpret_cast<unsigned char*>(_inbuf + _remained), _readPos, readn);

		_readPos += readn;
		size_t totalbytes = readn + _remained;
		setg(_inbuf, _inbuf, _inbuf + totalbytes / sizeof(char_type));

		_remained = totalbytes % sizeof(char_type);
		if(_remained != 0)
			_remainedchar = _inbuf[totalbytes / sizeof(char_type)];

		return this->sgetc();
	}

private:
	NSDataStreamBuffer(NSDataStreamBuffer const&);
	NSDataStreamBuffer& operator=(NSDataStreamBuffer const&);

	NSDataWrapper _nsdata;
	char_type * _inbuf;
	char_type * _outbuf;
	bool _ownsBuffers;
	std::streamsize _bufsize;
	
	size_t _readPos;
	size_t _remained;
	char_type _remainedchar;
};
////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////
template <class charT, class traits = std::char_traits<charT> >
class NSDataGenericStream :
	public std::basic_iostream<charT, traits>,
	private NSDataStreamBuffer<charT, traits>
{
public:
	explicit NSDataGenericStream(NSMutableData * data): 
		std::basic_iostream<charT, traits>(this),
		NSDataStreamBuffer<charT, traits>(data)
	{ }

private:
	NSDataGenericStream(NSDataGenericStream const&);
	NSDataGenericStream& operator=(NSDataGenericStream const&);
};
////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////
typedef NSDataGenericStream<char> NSDataStream;


