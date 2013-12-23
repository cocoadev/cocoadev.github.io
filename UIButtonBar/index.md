---
layout: page
title: UIButtonBar
---



The General/UIButtonBar initializer works with a list of items (withItemList: parameter). From what I can 
see, it's an General/NSArray (or something to responds to objectAtIndex:) of General/NSDictionary (or something 
that reponds to objectForKey:)

I built collection that logs objectForKey method calls and the only key I see coming is 
@"General/UIButtonBarButtonTarget".

This corresponds well with some constant strings defined in General/UIKit.framework:
General/UIButtonBarButtonTitleWidth
General/UIButtonBarButtonStyle
General/UIButtonBarButtonTarget
General/UIButtonBarButtonAction
General/UIButtonBarButtonTag
General/UIButtonBarButtonSelectedInfo
General/UIButtonBarButtonInfo
General/UIButtonBarButtonTitle
General/UIButtonBarButtonType

Maybe these are used in a fashion similar to General/NSAttributedString -- the dictionary defines the 
attributes of the button in the button bar. Oddly, there doesn't appear to a way to specify an 
instance of General/UIButtonBarButton or General/UIButtonBarTextButton. Also, how the hell do you put an 
@selector into a dictionary for General/UIButtonBarButtonAction?

Here is some stuff I've been trying:

General/UIButtonBarButton *testButton = General/[[[UIButtonBarButton alloc] initWithImage:General/[UIImage 
imageNamed:@"arrowup.png"] selectedImage:General/[UIImage imageNamed:@"arrowdown.png"] 
label:@"Test" labelHeight:20.0f withBarStyle:0 withStyle:0 withOffset:General/NSMakeSize(0.0f, 0.0f)] 
autorelease];

General/NSDictionary *dictionary = General/[NSDictionary dictionaryWithObjectsAndKeys:
		testButton, @"General/UIButtonBarButtonTarget",
		nil];

General/NSArray *itemList = General/[NSArray arrayWithObject:dictionary];

General/UIButtonBar *buttonBar = General/[[[UIButtonBar alloc] initInView:mainView withItemList:itemList] 
autorelease];
[buttonBar setBarStyle:1];
[buttonBar setDelegate:self];

All I get is the blank button bar at the bottom of the view.

Any thoughts or help here would be most appreciated.

----

Mobile Colloquy is using General/UIButtonBar. See http://mcolloquy.googlecode.com/svn/trunk/General/CQChatController.m

    
extern General/NSString *kUIButtonBarButtonAction;
extern General/NSString *kUIButtonBarButtonInfo;
extern General/NSString *kUIButtonBarButtonInfoOffset;
extern General/NSString *kUIButtonBarButtonSelectedInfo;
extern General/NSString *kUIButtonBarButtonStyle;
extern General/NSString *kUIButtonBarButtonTag;
extern General/NSString *kUIButtonBarButtonTarget;
extern General/NSString *kUIButtonBarButtonTitle;
extern General/NSString *kUIButtonBarButtonTitleVerticalHeight;
extern General/NSString *kUIButtonBarButtonTitleWidth;
extern General/NSString *kUIButtonBarButtonType;

General/NSDictionary *buttonItem = General/[NSDictionary dictionaryWithObjectsAndKeys:General/[CQConnectionsController defaultController], kUIButtonBarButtonTarget, @"showConnections", kUIButtonBarButtonAction, @"connections.png", kUIButtonBarButtonInfo, General/[NSNumber numberWithUnsignedInt:1], kUIButtonBarButtonTag, General/[NSValue valueWithSize:General/NSMakeSize(0., 2.)], kUIButtonBarButtonInfoOffset, nil];
General/NSArray *items = General/[NSArray arrayWithObjects:buttonItem, nil];
General/UIButtonBar *buttonBar = General/[[UIButtonBar alloc] initInView:contentView withFrame:General/CGRectMake(0., screenRect.size.height - 40., screenRect.size.width, 40.) withItemList:items];

int buttons[1] = { 1 };
[buttonBar registerButtonGroup:1 withButtons:buttons withCount:1];
[buttonBar showButtonGroup:1 withDuration:0.];


Note: General/UIButtonBar only works if it is in the contentView of the General/UIWindow. It will not intercept clicks if it in a subview of any other view.

----
I've figured out how to do text buttons for a General/UIButtonBar.  Use the same basic structure as the above Colloquy code, but the following dictionary will make a General/UIButtonBarTextButton instead:

    

        General/NSDictionary *buttonItem = General/[NSDictionary dictionaryWithObjectsAndKeys:
                //self, kUIButtonBarButtonTarget,
                //@"someSelector", kUIButtonBarButtonAction,
                General/[NSNumber numberWithUnsignedInt:1], kUIButtonBarButtonTag,
                General/[NSNumber numberWithUnsignedInt:3], kUIButtonBarButtonStyle,
                General/[NSNumber numberWithUnsignedInt:1], kUIButtonBarButtonType,
                @"Button!", kUIButtonBarButtonInfo,
                nil
        ];



----
Maps use the following description to create its bottom button bar:

    

    {
        General/UIButtonBarButtonAction = directionsEnabled; 
        General/UIButtonBarButtonInfo = <General/UIImage: 0x175d80>; 
        General/UIButtonBarButtonStyle = 1; 
        General/UIButtonBarButtonTag = 1; 
        General/UIButtonBarButtonTarget = <General/ButtonBar: 0x174aa0>; 
        General/UIButtonBarButtonType = 2; 
    }, 
    {
        General/UIButtonBarButtonAction = searchEnabled; 
        General/UIButtonBarButtonInfo = <General/UIImage: 0x175d80>; 
        General/UIButtonBarButtonStyle = 2; 
        General/UIButtonBarButtonTag = 2; 
        General/UIButtonBarButtonTarget = <General/ButtonBar: 0x174aa0>; 
        General/UIButtonBarButtonType = 2; 
    }, 
    {
        General/UIButtonBarButtonInfo = <General/UISegmentedControl: 0x174c10>; 
        General/UIButtonBarButtonTag = 3; 
        General/UIButtonBarButtonType = 3; 
    }, 
    {
        General/UIButtonBarButtonAction = trafficDisabled; 
        General/UIButtonBarButtonInfo = <General/UIImage: 0x175ee0>; 
        General/UIButtonBarButtonStyle = 2; 
        General/UIButtonBarButtonTag = 5; 
        General/UIButtonBarButtonTarget = <General/ButtonBar: 0x174aa0>; 
        General/UIButtonBarButtonType = 2; 
    }, 
    {
        General/UIButtonBarButtonAction = trafficEnabled; 
        General/UIButtonBarButtonInfo = <General/UIImage: 0x175ee0>; 
        General/UIButtonBarButtonStyle = 1; 
        General/UIButtonBarButtonTag = 4; 
        General/UIButtonBarButtonTarget = <General/ButtonBar: 0x174aa0>; 
        General/UIButtonBarButtonType = 2; 
    }



----
You may as well have a look at General/MobilScrobbler http://dev.c99.org/General/MobileScrobbler/ - they make heavy use of an iPod-Styled General/UIButtonBar.

    

- (General/NSArray *)buttonBarItems {
	        return [ General/NSArray arrayWithObjects:
	                                        [ General/NSDictionary dictionaryWithObjectsAndKeys:
	                                         @"buttonBarItemTapped:", kUIButtonBarButtonAction,
	                                         @"General/BarPodcasts.png", kUIButtonBarButtonInfo,
	                                         @"BarPodcasts_Sel.png", kUIButtonBarButtonSelectedInfo,
	                                         [ General/NSNumber numberWithInt: 1], kUIButtonBarButtonTag,
	                                         self, kUIButtonBarButtonTarget,
	                                         General/NSLocalizedString(@"Radio", @"Radio view"), kUIButtonBarButtonTitle,
	                                         @"0", kUIButtonBarButtonType,
	                                         nil 
	                                         ],
	                                       
	                                        [ General/NSDictionary dictionaryWithObjectsAndKeys:
	                                         @"buttonBarItemTapped:", kUIButtonBarButtonAction,
	                                         @"History.png", kUIButtonBarButtonInfo,
	                                         @"General/HistorySelected.png", kUIButtonBarButtonSelectedInfo,
	                                         [ General/NSNumber numberWithInt: 2], kUIButtonBarButtonTag,
	                                         self, kUIButtonBarButtonTarget,
	                                         General/NSLocalizedString(@"Charts", @"Charts view"), kUIButtonBarButtonTitle,
	                                         @"0", kUIButtonBarButtonType,
	                                         nil 
	                                         ],
	                                       
	                                        [ General/NSDictionary dictionaryWithObjectsAndKeys:
	                                         @"buttonBarItemTapped:", kUIButtonBarButtonAction,
	                                         @"Search.png", kUIButtonBarButtonInfo,
	                                         @"General/SearchSelected.png", kUIButtonBarButtonSelectedInfo,
	                                         [ General/NSNumber numberWithInt: 3], kUIButtonBarButtonTag,
	                                         self, kUIButtonBarButtonTarget,
	                                         General/NSLocalizedString(@"Search", @"Search view"), kUIButtonBarButtonTitle,
	                                         @"0", kUIButtonBarButtonType,
	                                         nil 
	                                         ],
	                                       
	                                        nil
	                                        ];
	}



And they have a good example on how to get the button tapps:

    

	- (void)buttonBarItemTapped:(id) sender {
	        int button = [ sender tag ];
	        if(button != _currentView) {
	                _currentView = button;         
	                switch (button) {
	                        case 1:
	                                [_transition transition:General/UITransitionFade toView:_radioListView];
	                                break;
	                        case 2:
	                                [_transition transition:General/UITransitionFade toView:_chartsView];
	                                break;
	                        case 3:
	                                [_transition transition:General/UITransitionFade toView:_radioSearchView];
	                                break;
	                }
	        }
	}



Hope this helps ;)
