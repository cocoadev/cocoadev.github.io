---
layout: page
title: MemoryAllocationNightmare
---

I am fairly new to IOS Dev so please bear with me.  I am using X Code 4, and I have tried to upgrade my project to IOS 5, although I think that has little to do with anything.  I am calling an Odata Service that is returning data and I am calling [self.TableName reloadData].  The debugger does NOT get into the Cell Creation method...It just pops back out to the main application position and gives me a SigKill message or if I click on the arrow to keep debugging, I get the EXC_BAD_ACCESS method.  I know I am probably autoreleasing wrong or incorrectly...I've seen notes on using Zombies, but I don't think I enabled it correctly because it did not connect until I "attached to process" and pointed it at xcode.  It looked like it was doing something, but I didn't see anything that pointed to my bad objects...I have included my code below.  Any help would be greatly appreciated.

thanks

mark


#import "CustomTalentSearchController.h"
#import "CAEntities.h"
#import "TalentDetailController.h"

@implementation CustomTalentSearchController

@synthesize talentSearchResults;
@synthesize talentSearchResultsTable;
@synthesize talentTitleTextField, talentAgentTextField, talentAgencyTextField;
@synthesize searchProgressTimer, searchProgressView, searchTalentProgressLabel, searchResultCountLabel;
@synthesize currentProgress;

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        // Custom initialization
    }
    return self;
}

-(void)showSearchProgress
{
    self.searchProgressView.hidden = NO;
    self.searchTalentProgressLabel.hidden = NO;
}

-(void)hideSearchProgress
{
    self.searchProgressView.hidden = YES;
    self.searchTalentProgressLabel.hidden = YES;
}

-(void)initiateSearchProgress
{
    [self.searchProgressView setProgress:0.0];
    [self showSearchProgress];
    self.currentProgress = 0.0;
}

-(void)startTimer
{
    [self.searchProgressTimer invalidate];
    self.searchProgressTimer = [NSTimer scheduledTimerWithTimeInterval: 5.0
                                                                target: self
                                                              selector: @selector(handleTimer:)
                                                              userInfo: nil
                                                               repeats: YES];
}

-(void)stopTimerAndProgressView
{
    [self hideSearchProgress];
    
    [self.searchProgressTimer invalidate];
    self.searchProgressTimer = nil;
    
}

-(void)searchTalent:(NSDictionary *)args
{
    //---------------------------------
    //Create a proxy to the service...
    //---------------------------------
    CAEntities *proxy;
    @try 
    {
        
        proxy = [[CAEntities alloc] initWithUri:@"http://SERVERNAMEANDPORT/WCFODATASERVICE.svc/" credential:nil];
        
        
        //[self performSelectorOnMainThread:@selector(initiateSearchProgress) withObject:nil waitUntilDone:NO];
        //[self performSelectorOnMainThread:@selector(startTimer) withObject:nil waitUntilDone:NO];
        
        DataServiceQuery *qry = [proxy talents];
        
        //BUILDING A FILTER STRING FOR SEARCHING TALENT ON THE SERVER...
        NSMutableString* filterString = [NSMutableString stringWithString: @"substringof('"];
        
        NSString *searchName = [args objectForKey:@"searchName"];
        NSString *searchAgent = [args objectForKey:@"searchAgent"];
        NSString *searchAgency = [args objectForKey:@"searchAgency"];
        
        if([searchName length] > 0)
        {
            [filterString appendString: searchName];
            [filterString appendString:@"',name)"];
            
        }
        
        if([searchAgent length] > 0)
        {
            if([searchName length] > 0)
            {
                [filterString appendString:@" and substringof('"];
            }
            [filterString appendString: searchAgent];
            [filterString appendString:@"',Agent/name)"];
            
        }
        
        if([searchAgency length] > 0)
        {
            if([searchName length] > 0 || [searchAgent length] > 0)
            {
                [filterString appendString:@" and substringof('"];
            }
            [filterString appendString: searchAgency];
            [filterString appendString:@"',Agency/name)"];
        }
        
        [qry filter:filterString];
        //WHAT "substringof" looks like with multiple filters...
        //[qry filter:@"substringof('mark',name) and substringof('boo',name)"];
        
        [qry orderBy:@"name"];
        
        [qry expand:@"Credits/Show,Credits/Craft,Credits/Show/Genre,Credits/Show/Studio,Strengths/Genre,Agency,Agent"];
        
        QueryOperationResponse *result = [qry execute];
        
        self.talentSearchResults = result getResult] retain];
        
       //RELOAD THE TABLEVIEW DATA...
        [self.talentSearchResultsTable reloadData];
        self.searchResultCountLabel.text = [[[NSString stringWithFormat:@"%d", [talentSearchResults count]];
                
    }
    @catch(DataServiceRequestException *exception)
    {
        [self showErrorMessage:exception];
    }
    @finally {
        //cleanup
        //REMMED OUT THE PROXY RELEASE TO SEE IF THAT WOULD HELP...IT DIDN'T...
        //[proxy release];
        
    }
    
}

//NOT CALLING THIS METHOD CURRENTLY...
-(void)loadSearchResults
{
    
    @try {
        if([talentSearchResults count] > 0)
        {
            //Have to refresh the Data in the Table...
            //self.talentSearchResults = searchResults;
            self talentSearchResultsTable] reloadData];
            self.searchResultCountLabel.text = [[[NSString stringWithFormat:@"%d", [talentSearchResults count]];
            
            [self stopTimerAndProgressView];
        }
        else
        {
            //Show Simple Alert to let Viewer know they need to search again...
            UIAlertView* alertView = [[UIAlertView alloc] initWithTitle:@"Message Center"
                                                                message:@"0 Search Results.  Please refine your search" delegate:self 
                                                      cancelButtonTitle:@"OK" otherButtonTitles:nil];
            [alertView show];
            [alertView release];
        }

    }
    @catch (NSException *exception) {
        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error Message Center"
                                                            message:exception.reason delegate:self 
                                                  cancelButtonTitle:@"OK" otherButtonTitles:nil];
        [alertView show];
        [alertView release];    
    }
    @finally 
    {
        //nothing currently...
    }
}

-(void)showErrorMessage:(DataServiceRequestException *)exception
{
    NSString *errMessage = [exception reason];
    //NSLog(errMessage);
    UIAlertView* alertView = [[UIAlertView alloc] initWithTitle:@"Message Center"
                                                        message:errMessage
                                                       delegate:self 
                                              cancelButtonTitle:@"OK" otherButtonTitles:nil];
    [alertView show];
    [alertView release];
    
    [self stopTimerAndProgressView];
}

-(void)handleTimer:(NSTimer*)theTimer
{
    
    if(currentProgress < 1.0)
    {
        currentProgress += .1;
        self.searchProgressView.progress = currentProgress;
    }
    
}

//NOT USING THIS YET...USING THE ENTER KEY FOR SEARCHING CURRENTLY>>>>
-(IBAction)searchButtonClicked
{
    NSString *searchName = talentTitleTextField.text;
    NSString *searchAgent = talentAgentTextField.text;
    NSString *searchAgency = talentAgencyTextField.text;
    
    NSDictionary *args = [NSDictionary dictionaryWithObjectsAndKeys:
                          searchName, @"searchName",
                          searchAgent, @"searchAgent",
                          searchAgency, @"searchAgency",
                          nil];
    
    [self performSelectorInBackground:@selector(searchTalent:) withObject:args];
    //[self searchTalent];
    
}

//USING THIS METHOD TO SEARCH TALENT WHEN THE USER CLICKS THE ENTER KEY>>>>
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange: (NSRange)range replacementString: (NSString *)string     
{
    
    if ([string isEqualToString:@"\n"]) 
    {
        NSString *searchName = talentTitleTextField.text;
        NSString *searchAgent = talentAgentTextField.text;
        NSString *searchAgency = talentAgencyTextField.text;
        
        NSDictionary *args = [NSDictionary dictionaryWithObjectsAndKeys:
                              searchName , @"searchName",
                              searchAgent, @"searchAgent",
                              searchAgency, @"searchAgency",
                              nil];
        [self initiateSearchProgress];
        [self startTimer];
        //[self performSelectorInBackground:@selector(searchTalent:) withObject:args];
        [self searchTalent:args];
        
    }
    
    return YES;
    
}

- (void)dealloc
{
    [talentSearchResultsTable release], talentSearchResultsTable = nil;
    [talentSearchResults release], talentSearchResults = nil;
    [talentTitleTextField release], talentTitleTextField = nil;
    
    [super dealloc];
}

- (void)didReceiveMemoryWarning
{
    // Releases the view if it doesn't have a superview.
    [super didReceiveMemoryWarning];
    
    // Release any cached data, images, etc that aren't in use.
}

#pragma mark - View lifecycle

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
}

- (void)viewDidUnload
{
    [super viewDidUnload];
    // Release any retained subviews of the main view.
    // e.g. self.myOutlet = nil;
}

- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
    // Return YES for supported orientations
	return YES;
}
#pragma mark - Table view data source

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    // Return the number of rows in the section.
    return [self.talentSearchResults count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";
    
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]autorelease];
    }
    
    // Configure the cell...
    
    CAModel_Talent *talent = [self.talentSearchResults objectAtIndex:indexPath.row];
    
    NSMutableString *talentRowText = [NSMutableString stringWithString:talent.m_name];
    
    if(talent.m_Agent !=nil)
    {
        [talentRowText appendString:@", Agent:  "];
        CAModel_Agent *agent = (CAModel_Agent*)[talent.m_Agent objectAtIndex:0];
        
        [talentRowText appendString:agent.m_name];
    }
    
    if(talent.m_Agency != nil)
    {
        [talentRowText appendString:@", Agency:  "];
        CAModel_Agency *agency = (CAModel_Agency*)[talent.m_Agency objectAtIndex:0];
        [talentRowText appendString:agency.m_name];
    }
    
    cell.textLabel.text = talentRowText;
    
    cell.accessoryType = UITableViewCellAccessoryDetailDisclosureButton;
    
    return cell;
}

#pragma mark - Table view delegate

//NOT GETTING THIS FAR YET...CODE BLOWS UP WHEN TRYING TO RENDER THE TABLEVIEW>>>>
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    TalentDetailController *talentDetailController = [[TalentDetailController alloc] initWithNibName:@"TalentDetailController" bundle:nil];
    
    talentDetailController.talentDetail = [self.talentSearchResults objectAtIndex:indexPath.row];
    
    [self.navigationController pushViewController:talentDetailController animated:YES];
    
    [talentDetailController release];
    
}

@end

----

This is not a maiing list. It's meant to be a library of helpful pages a la Wikipedia. Please read the rules and don't pollute the wiki.

