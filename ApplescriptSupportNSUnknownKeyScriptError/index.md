---
layout: page
title: ApplescriptSupportNSUnknownKeyScriptError
---



Hello-
I am attempting to support applescript in my recipe management application that has a simple structure.  The applications delegate class, CWGMainController, supports applecsript with the following methods:

    
@implementation CWGMainController (CWGScriptability) 
- (NSScriptObjectSpecifier *)objectSpecifier;
{
    NSScriptClassDescription *containerClassDesc = (NSScriptClassDescription *)[NSScriptClassDescription classDescriptionForClass:[NSApp class]];
    NSNameSpecifier *nameSpecifier = [[NSNameSpecifier alloc] initWithContainerClassDescription:containerClassDesc containerSpecifier:nil key:@"recipe"];
    
    return nameSpecifier;
}
- (BOOL)application:(NSApplication *)sender delegateHandlesKey:(NSString *)key { 
    if ([key isEqual:@"recipe"] || [key isEqual:@"recipes"]) {
        return YES;
    } else {
        return NO;
    }
}
- (void)insertInRecipes:(CWGRecipe *)newRecipe;
{
    recipeController xmlParser] addRecipe:newRecipe];
}
@end


Now, the recipe class, [[CWGRecipe, also has scripting support:
    
@implementation CWGRecipe (CWGscriptability) 
- (NSScriptObjectSpecifier *)objectSpecifier;
{ 
    NSApplication *mainApplication = [NSApplication sharedApplication];
    CWGMainController *mainController = [mainApplication delegate];
    CWGRecipeController *recipeController = [mainController recipeController];
    NSArray *recipes = [recipeController recipesArray];
    unsigned int recipeIndex = [recipes indexOfObjectIdenticalTo:self];
    
    if (recipeIndex != NSNotFound) {
	NSScriptObjectSpecifier *containerSpecifier = [mainController objectSpecifier];
	NSIndexSpecifier *indexSpecifier = [[NSIndexSpecifier allocWithZone:[self zone]] initWithContainerClassDescription:[containerSpecifier keyClassDescription] containerSpecifier:containerSpecifier key:@"application"];
	
	return [indexSpecifier autorelease];
    }
    
    NSLog(@"I did not find this recipe in the recipes array, error!");
    return nil;
} 
@end


my sdef file looks like this:
    
<suite name="Organized Gourmet Suite" code="OGAP" description="Terms and Events for controlling Organized Gourmet">
		<class name="application" code="capp" description="The Organized Gourmet application">
			<cocoa class="NSApplication"/>
			<element description="recipes" type="recipe">
				<cocoa key="recipes"/>
			</element>
		</class>
		<class name="recipe" code="OGre" description="A recipe." plural="recipes">
			<cocoa class="CWGRecipe"/>
			<element type="ingredient"/>
			<element type="recipe"><cocoa key="recipe"/></element>
			<property name="name" code="OGrn" description="The recipe name" type="text">
				<cocoa key="recipeName"/>
			</property>
		</class>
	</suite>

note that I am not showing the complete sdef file.  It contains the standard suite...

So, now if I run a simple script like this:
    
tell application "Organized Gourmet"
	make new recipe with properties {name:"recipe from applescript"}
end tell

the script returns a "NSUnknownKeyScriptError".  Now for the crazy part: the script does what it is supposed to.  'application:delegateHandlesKey:' is called and then 'insertInRecipes:' is called.  Therefore, the recipe is created and inserted into the correct array.  Neither of the objectSpecifiers is called. 

I believe that the script is expecting a return specifier but, since those functions are not being called, I am a bit confused.  Any ideas?

-Ryan

----
so, it appears as though the problem is in the line recipeController xmlParser] addRecipe:newRecipe];
If I remove the line, the error goes away, but so does the functionality.  Interestingly, the next call is to objectSpecifier: in the [[CWGRecipe class.

-Ryan

