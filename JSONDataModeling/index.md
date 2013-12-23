---
layout: page
title: JSONDataModeling
---

I currently am working on developing a set of libraries for work to handle communication with Twitter. I have most of the twitter communication with twitter sorted out and working properly. The issue I am having is this, I am working on creating native data models for the returned data so that the rest of the people I work with don't have to know a damn thing about what the twitter API returns as JSON data. I generally create my data models like this: Create a class that represents the data returned, where each attribute maps to a ObjC property. I add an initWithDictionary/initWithArray method that I use to translate the dictionary or array into an object. Generally the initWithDictionary method just calls [self setValuesForKeysWithDictionary:] and everything gets taken care of. Its lean, its mean and its quick to code. The problem I run into is when I have fields that are arrays or other objects? How do I get my code to walk down the tree of data instantiating objects as need be.


Here is an example of my code

    
@class TwitterUser;

@interface TwitterUserResults : NSObject {
	NSMutableArray *_users; // Array of TwitterUser objects
}

- (id)initWithArray:(NSArray *)array;
- (NSInteger)countOfUsers;
- (TwitterUser *)objectInUsersAtIndex:(NSInteger)index;

@end


    
@implementation FMTwitterUserResults

#pragma mark -
#pragma mark Instance Methods
- (id)initWithArray:(NSArray *)array {
	if(self = [super init]) {
		self->_users = [[NSMutableArray alloc] init];
		
		for(NSDictionary *dict in array) {
			[self->_users addObject:[[TwitterUser alloc] initWithDictionary:dict]];
		}
	}
	return self;
}

- (NSInteger)countOfUsers {
	return [self->_users count];
}

- (TwitterUser *)objectInUsersAtIndex:(NSInteger)index {
	TwitterUser *user = [self->_users objectAtIndex:index];
	
	return [user autorelease];
}

@end


    
@class TwitterTweet;

@interface TwitterUser : NSObject {
	NSNumber *_contributers_enabled;
	NSString *_created_at;
	NSString *_description;
	NSNumber *_favourites_count;
	NSNumber *_follow_request_sent;
	NSNumber *_followers_count;
	NSNumber *_following;
	NSNumber *_friends_count;
	NSNumber *_geo_enabled;
	NSNumber *_id;
	NSString *_lang;
	NSString *_location;
	NSString *_name;
	NSNumber *_notifications;
	NSString *_profile_background_color;
	NSString *_profile_background_image_url;
	NSNumber *_profile_background_tile;
	NSString *_profile_image_url;
	NSString *_profile_link_color;
	NSString *_profile_sidebar_border_color;
	NSString *_profile_sidebar_fill_color;
	NSString *_profile_text_color;
	NSNumber *_profile_use_background_image;
	NSNumber *_protected;
	NSString *_screen_name;
	TwitterTweet *_user_status;
	NSNumber *_statuses_count;
	NSString *_time_zone;
	NSString *_url;
	NSString *_utc_offset;
	NSNumber *_verified;
}
@property (nonatomic, copy) NSNumber *contributors_enabled;
@property (nonatomic, copy) NSString *created_at;
@property (nonatomic, copy) NSString *description;
@property (nonatomic, copy) NSNumber *favourites_count;
@property (nonatomic, copy) NSNumber *follow_request_sent;
@property (nonatomic, copy) NSNumber *followers_count;
@property (nonatomic, copy) NSNumber *following;
@property (nonatomic, copy) NSNumber *friends_count;
@property (nonatomic, copy) NSNumber *geo_enabled;
@property (nonatomic, copy) NSNumber *id;
@property (nonatomic, copy) NSString *lang;
@property (nonatomic, copy) NSString *location;
@property (nonatomic, copy) NSString *name;
@property (nonatomic, copy) NSNumber *notifications;
@property (nonatomic, copy) NSString *profile_background_color;
@property (nonatomic, copy) NSString *profile_background_image_url;
@property (nonatomic, copy) NSNumber *profile_background_tile;
@property (nonatomic, copy) NSString *profile_image_url;
@property (nonatomic, copy) NSString *profile_link_color;
@property (nonatomic, copy) NSString *profile_sidebar_border_color;
@property (nonatomic, copy) NSString *profile_sidebar_fill_color;
@property (nonatomic, copy) NSString *profile_text_color;
@property (nonatomic, copy) NSNumber *profile_use_background_image;
@property (nonatomic, copy) NSNumber *protected;
@property (nonatomic, copy) NSString *screen_name;
@property (nonatomic, retain) TwitterTweet *userStatus;
@property (nonatomic, copy) NSNumber *statuses_count;
@property (nonatomic, copy) NSString *time_zone;
@property (nonatomic, copy) NSString *url;
@property (nonatomic, copy) NSString *utc_offset;
@property (nonatomic, copy) NSNumber *verified;

- (id)initWithDictionary:(NSDictionary *)dictionary;

@end


    
@implementation TwitterUser

#pragma mark -
#pragma mark Synthesized Properties
@synthesize contributors_enabled = _contributors_enabled;
@synthesize created_at = _created_at;
@synthesize description = _description;
@synthesize favourites_count = _favourites_count;
@synthesize follow_request_sent = _follow_request_sent;
@synthesize followers_count = _followers_count;
@synthesize following = _following;
@synthesize friends_count = _friends_count;
@synthesize geo_enabled = _geo_enabled;
@synthesize id = _id;
@synthesize lang = _lang;
@synthesize location = _location;
@synthesize name = _name;
@synthesize notifications = _notifications;
@synthesize profile_background_color = _profile_background_color;
@synthesize profile_background_image_url = _profile_background_image_url;
@synthesize profile_background_tile = _profile_background_title;
@synthesize profile_image_url = _profile_image_url;
@synthesize profile_link_color = _profile_link_color;
@synthesize profile_sidebar_border_color = _profile_sidebar_border_color;
@synthesize profile_sidebar_fill_color = _profile_sidebar_fill_color;
@synthesize profile_text_color = _profile_text_color;
@synthesize profile_use_background_image = _profile_use_background_image;
@synthesize protected = _protected;
@synthesize screen_name = _screen_name;
@synthesize userStatus = _user_status;
@synthesize statuses_count = _statuses_count;
@synthesize time_zone = _time_zone;
@synthesize url = _url;
@synthesize utc_offset = _utc_offset;
@synthesize verified = _verified;

#pragma mark -
#pragma mark Instance Methods
- (id)initWithDictionary:(NSDictionary *)dictionary {
	if(self = [super init]) {
		[self setValuesForKeysWithDictionary:dictionary];
	}
	return self;
}

- (void)setStatus:(NSDictionary *)dict {
	self.userStatus = [[TwitterTweet alloc] initWithDictionary:dict];
}

@end


So my question is how do I solve this problem of instantiating relations between objects and keep every thing KVC compliant?

I've read most of Apples Docs on KVC, but I don't understand how to properly set it up so that setValuesForKeysWithDictionary: will call a method like addUsersObject: for each sub-tree of a trend object in the returned JSON object. Any advice would be helpful.

Thanks
-Laveur

----

Not that I've thought about this deeply, but� how about writing setter accessors for those attributes that require special handling? I would expect setValue:forKey to call a written accessor for a property if you write one. There are a few ways to do that - use the suggested naming pattern and skip @synthesize, or I think you can @synthesize the getter and specify the setter in the @property declaration.  -- PaulCollins

