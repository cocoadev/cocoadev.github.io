---
layout: page
title: AModestUninstallingProposal
---

A small (partial) implementation of what follows, under a BSD license, is available at http://millenomi.altervista.org/Extras/Claims to be used as a basis for uninstallers.

----

**A short abstract:** application removal on OS X leaves files (preferences, application support folders...) behind. I propose using a standardized way to "claim" files that would be left behind, so that uninstallers can remove them without having to guess, as they currently do.

----

Installing apps on Mac OS X, be they Cocoa or not, is usually dead-brain-monkey easy (well, OK, there's the initial shock of the new volume popping up on a total newbie's desktop, but that's it).

Uninstalling, not so much. It leaves files behind. So much, in fact, that uninstaller applications exist on Mac OS X (like AppZapper).

The following is a proposal to end or at least mitigate the madness. Do with it what you will, discuss it or implement it (or ignore it outright, but I think that'd be more harmful than anything else).

In the following:


* With *bundle* I mean a bundle as defined by Mac OS X terminology (ie a folder that is treated as a single filesystem object in the Finder) that the user wants to remove. In the following, "bundle" will usually be an application or Installer package, but need not be (ie for preference panes or widgets). Also, I will assume that the bundle implements the provisions below, unless specified otherwise.
* With *uninstaller* I mean a part of a software program whose purpose is removing an application, as instructed by an user (for example, the aforementioned AppZapper, or, one hopes for the future, the Finder move-to-trash code itself).


The specification is broken in two parts: one regarding bundles of all kinds, and one regarding Installer packages (which are treated separately). The second part is harder to implement, and the first one (or at least part of it) is the most "urgent" part to implement. It requires no code on the part of the provider of the bundle (ie you, the software developer), but it requires creating or modifying an uninstaller in order to understand it.

----

*Part one: bundles that are not Installer packages.*

To specify which objects may be created by the bundle without the user's knowledge, that an uninstaller might want to remove, the bundle *lays claim* to those objects by specifying them in its InfoPlist file inside an additional key of the info dictionary. When an uninstaller is pointed to a bundle, it looks within the info dictionary to see whether it is compliant (ie has the additional key in its InfoPlist). If it is, it then proposes to remove the bundle and **only** any claimed objects (in the form of the files where those objects are stored, if they aren't filesystem objects). If it isn't, it may proceed with empirical methods as it is common for today's uninstallers.

To comply with this proposal, a bundle adds a key named **L0ClaimInformation** to its InfoPlist (using my own 'ell-zero' prefix from ChooseYourOwnPrefix). The value must be a dictionary whose keys are the following:


* **L0Claims**: an array that includes one or more claims, dictionaries whose format is specified below.
* **L0ShouldRemoveInstead**: a dictionary in the claim format that is used to point the uninstaller to another bundle. If present, the uninstaller should then offer to remove the pointed-to bundle rather than the one the user offered. This should be used in application bundles that are installed from a package, to point the uninstaller to the package instead. If used and the other bundle is found, all other claims in this bundle are ignored.
* **L0DoNotIncludeDefaultClaims**: a boolean that tells the uninstaller whether it should not include the default claims specified below. Defaults to NO, meaning to include the default claims.


The claims array contains dictionaries with a *L0ClaimType* key whose value is a string that specifies the type of object being claimed. The types are specified below, and after each of them the keys that can be used to indicate more details about the claim.

**path**: A path claim indicates that the application has a path that the user is unaware of that should be removed along with it. It uses the following keys:

* **L0Domain**: an array of any number of the strings: "system", "local", "network" or "user". Specifies the domain(s) the path may be located in. "system" is used for the System domain (/System), "local" for the local domain (/), "network" for the network domain (/Network) and "user" for all user domains (not just the current user's). Required if a L0SpecialFolder is not indicated.
* **L0SpecialFolder**: a string, indicating a special folder. It must be a FourCC specified in the Apple docs and usable for FSFindFolder ( http://developer.apple.com/documentation/Carbon/Reference/Folder_Manager/Reference/reference.html#//apple_ref/c/tdef/FolderType ). For example, specify "sdat" for the /Users/Shared folder. L0Domain may be specified along with this key if a specific search domain is required.
* **L0Path**: finally, a string, a POSIX path (relative to the domains specified above) that indicates what file, folder or other filesystem object you claim. If it's found in more than one of the domains specified in L0Domain, all the found objects are claimed.


An example follows (in OpenStep plist format):

    

L0Claims = (
	{
		L0ClaimType = "path";
		L0Domain = ( "user", "local" );
		L0Path = "Application Support/My Great App"; /* claims /Library/Application Support/My Great App and
		~/Library/Application Support/My Great App */
	},
	{
		L0ClaimType = "path";
		L0SpecialFolder = "sdat"; /* /Users/Shared */
		L0Path = "My Great App"; /* claims /Users/Shared/My Great App */
	}
);



**bundle**: A bundle claim indicates that this software uses a bundle (outside of its own contents) that should be removed regardless of its current location. It uses the following keys:

* **L0Identifier**: A string, the bundle ID for the bundle. Required.
* **L0BundleUTI**: A string, an UTI for the bundle that can be used to direct the uninstaller to folders where that bundle might be located. Optional, defaults to "com.apple.application-bundle".


An example follows:

    

L0Claims = (
	{
		L0ClaimType = "bundle";
		L0Identifier = "com.mysite.MyGreatApp.SupportAgent";
	},
	{
		L0ClaimType = "bundle";
		L0Identifier = "com.mysite.MyGreatApp.PreferencePane";
		L0BundleUTI = "com.apple.systempreference.prefpane";
	}
);



**preferences**: A preferences claim indicates that this software uses a preferences (NSUserDefaults) domain that should be removed along with it. It uses the following key:

* **L0Identifier**: A string, the identifier used for the preferences (defaults) domain.


An example follows:

    

L0Claims = ({ L0ClaimType="preferences"; L0Identifier="com.mysite.MyGreatApp.Registration"; });



**
An uninstaller should always presume, for widget and application bundles, that a preferences claim exists with an identifier equal to the bundle's identifier (with the appropriate changes for widgets).
**
This is for simplicity; this behaviour can be suppressed by using *L0DoNotIncludeDefaultClaims*, as specified above. To indicate that you only want to use the default claims, you still must include the *L0ClaimsInformation* key, to show the uninstaller that you comply with this specification; for example:

    

L0ClaimsInformation = { L0DoNotIncludeDefaultClaims = NO; };



**launchd-agent** and **launchd-daemon**: A launchd agent or daemon claim is only followed on Mac OS X 10.4 or later. It indicates that a specified launchd agent or daemon configuration item is to be removed along with this application. It uses the following keys:

* **L0Identifier**: A string, the identifier used as the launchd configuration file name.
* **L0Domain**: An array with the same semantics and contents as the one specified for path claims, indicates the domains to be searched for the agent or daemon configuration file.


*Using substitution*: You can specify that your app should not removed "per se", but instead you might point to a "master" bundle that should be removed in its place (for example, an Installer package). To do so, specify a claim to that bundle as a dictionary assigned to the **L0ShouldRemoveInstead** key, as follows:

    

L0ClaimInfo = {
	L0ShouldRemoveInstead = {
		L0ClaimType = "bundle";
		L0Identifier = "com.mysite.MyGreatApp.InstallerPackage";
		L0BundleUTI = "com.apple.installer-package";
	};
};



You can use path or bundle claims; if you use a path claim to specify a bundle, you should make sure it resolves to a single bundle. The uninstaller may ask the user or fail substitution if this doesn't happen. If the bundle cannot be found, the removal should continue with the original bundle as though L0ShouldRemoveInstead wasn't specified.

----

*Part 2: Installer packages*

An Installer package (of which we assume we're using the receipt copy left by Installer in {~,}/Library/Receipts) may have any number of claims in its Info.plist file, as described above, for files it did not install; it also claims all files in its bill of materials (installed by Installer).

In addition, an Installer package may have two scripts named "preremove" and "postremove" in its Resources folder. The uninstaller should execute them respectively before and after removal with the authorization used for the original installation (specified in the InfoPlist file for the package) with the arguments and environment variables specified for install scripts in "http://developer.apple.com/documentation/DeveloperTools/Conceptual/SoftwareDistribution/index.html", except $2 and $3 are equal to the empty string "". The uninstaller is responsible for creating the directories for those variables, if not yet existing. (For removal scripts only, $RECEIPT_PATH might not be a subdirectory of $INSTALLER_TEMP).

----

*Appendix A: Uninstaller behaviour*

An uninstaller removing a bundle should **only** remove files claimed by the bundle; it should not remove non-claimed files. (It might choose to not remove claimed files, or ask for further confirmation, if a claim appears to be dangerous; for example, an application installed within a user's home folder that claims something in the system domain that is actually found.)

An uninstaller might revert to an empirical behaviour for non-compliant bundles, but **shall never revert to such a behaviour** for bundles with the *L0ClaimInformation* key (that is, its behaviour should always be deterministic for these bundles).

----

*Appendix B: Two examples*

If the only file you create is your preferences file, just copy and paste the following to your Info.plist:

    
<!-- ... the beginning of Info.plist ... -->
<key>L0ClaimInformation</key>
<dict>
	<key>L0Claims</key>
	<dict>
		<key>L0DoNotIncludeDefaultClaims</key>
		<false />
	</dict>
</dict>
<!-- ... the rest of Info.plist ... -->


For a slightly more complex example, the following would have been used in Afloat 1.0b3, a preference pane:

    
<key>L0ClaimInformation</key>
<dict>
	<key>L0Claims</key>
	<array>
		<dict>
			<key>L0ClaimType</key>
			<string>path</string>
			<key>L0Domain</key>
			<array>
				<string>user</string>
			</array>
			<key>L0Path</key>
			<string>Application Support/SIMBL/Plugins/Afloat.bundle</string>
		</dict>
		<dict>
			<key>L0ClaimType</key>
			<string>preferences</string>
			<key>L0Identifier</key>
			<string>org.altervista.millenomi.Afloat</string>
		</dict>
	</array>
</dict>


 -- EmanueleVulcano aka millenomi

----

The above-mentioned security risk of an app claiming files that don't belong to it shouldn't be underestimated.

As a minimum safety measure one might want to ask Launch Services whether the app has ever been launched. If it hasn't, there's no point in uninstalling support files (how could the app have created any, anyway?). And this also prevents a malicious app you never launched to cause damage when you try to delete it.

If the uninstaller always runs as the current user (instead of as root or so), the uninstaller can't do any damage the app itself couldn't have caused when it was run, so I guess it's a security risk that's irrelevant.

Of course, if the app claims it had installed any setuid-root files or whatever, your uninstaller would have to ask for a password, and at that point it might be a good idea to tell the user what files are involved to prevent the worst exploits. Still, I'd probably say that who needs to install setuid-root files or the likes should probably use an Installer.app package.

One thing I haven't seen mentioned here at all: What happens when an application is deleted that several users on the computer worked with? Will it authenticate for each user and delete the files there, too?

-- UliKusterer

