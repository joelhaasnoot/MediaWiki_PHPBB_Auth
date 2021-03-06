MediaWiki_PHPBB_Auth
====================

This extension links MediaWiki to phpBB's user table for authentication, and disallows the creation of new accounts in MediaWiki. Users must then log in to the wiki with their phpBB account.

MediaWiki Page: http://www.mediawiki.org/wiki/Extension:PHPBB/Users_Integration

REQUIREMENTS
=================

* PHP5, works with PHP 7.x
* MySQL 5
* MediaWiki 1.11+ (tested with 1.31 where it's deprecated, but 1.33 removes auth plugins?)
* phpBB 3.2

INSTALL:
=================

Create a group in phpBB for your wiki users. I named mine "Wiki".   
You will need to put the name you choose in the code below.   

**PHPBB GROUP NOTE**: In order for a user to be able to use the wiki they will need to  
be a member of the group you made in the step above.

* Put Auth_phpBB.php in /extensions/Auth_phpBB  
* Put iAuthPlugin.php in /extensions/Auth_phpBB  
* Put PasswordHash.php in /extensions/Auth_phpBB  

### OPTIONAL PHPBB-TO-MEDIAWIKI USERNAME TRANSLATION FEATURE:  
phpBB usernames can be translated to more restrictive wiki usernames.  
Use phpBB ACP, Users and Groups, Custom profile fields.  
Create a "Single text field" custom profile field.  

**Suggested settings:**  
* Name it "wikiusername"  
* Set "Publicly display profile field" = no  
* Uncheck all visibility options, except check "Hide profile field"   
* Set "Field name/title presented to the user" = "Wiki Username"  
* Set "Field description" = "Forum username translated for wiki username restrictions"  
* Set "Length of input box" = 20  
* Set "Maximum number of characters" = 255  
* Set "Field validation" to "Any character"  
* Set Language definitions fields to same as field name/title/description above.  

**PROFILE NOTE:**  
The custom profile field must be hidden to all but the admins  
because users could otherwise hijack wiki accounts by entering any  
username they wish.  

**PROFILE NOTE:**  
Enter a valid MediaWiki username into this field in the user's  
profile only when the phpBB username conflicts with MediaWiki username restrictions.  
For example, enter "Under Score" for a user with the name "Under_Score" because  
underscores are not allowed in MediaWiki usernames.  All users with phpBB usernames  
which are also valid MediaWiki usernames do not need this field set.  

### Code

Open LocalSettings.php. Put this at the bottom of the file. Edit as needed.  

    /*-----------------[ Everything below this line. ]-----------------*/
    
    // phpBB User Database Plugin. (Requires MySQL Database)
    require_once "$IP/extensions/Auth_phpBB/Auth_phpBB.php";
    
    $wgAuth_Config = array(); // Clean.
    
    $wgAuth_Config['UseCanonicalCase'] = true;      // Setting this to true causes the MediaWiki usernames
                                                    // to match the casing of the phpBB ones (except with
                                                    // the first letter set uppercase.)
                                                    // Setting this to false causes usernames to be all
                                                    // lowercase except for the first character.
                                                    // Before June 2016 this setting was always false,
                                                    // changing it to true on an install where it previously
                                                    // was false will cause users with uppercase characters
                                                    // to appear as separate users from their previous
                                                    // all-lowercase account.
        
    $wgAuth_Config['WikiGroupName'] = 'Wiki';       // Name of your phpBB group
                                                    // users need to be a member
                                                    // of to use the wiki. (i.e. wiki)
                                                    // This can also be set to an array 
                                                    // of group names to use more then 
                                                    // one. (ie. 
                                                    // $wgAuth_Config['WikiGroupName'][] = 'Wiki';
                                                    // $wgAuth_Config['WikiGroupName'][] = 'Wiki2';
                                                    // or
                                                    // $wgAuth_Config['WikiGroupName'] = array('Wiki', 'Wiki2');
                                                    // )
    
    
    $wgAuth_Config['UseWikiGroup'] = true;          // This tells the Plugin to require
                                                    // a user to be a member of the above
                                                    // phpBB group. (ie. wiki) Setting
                                                    // this to false will let any phpBB
                                                    // user edit the wiki.
    
    $wgAuth_Config['UseExtDatabase'] = false;       // This tells the plugin that the phpBB tables
                                                    // are in a different database then the wiki.
                                                    // The default settings is false.
    
    $wgAuth_Config['MySQL_Host']        = 'localhost';      // phpBB MySQL Host Name.
    $wgAuth_Config['MySQL_Port']        = '';               // phpBB MySQL Port number.
    $wgAuth_Config['MySQL_Username']    = 'username';       // phpBB MySQL Username.
    $wgAuth_Config['MySQL_Password']    = 'password';       // phpBB MySQL Password.
    $wgAuth_Config['MySQL_Database']    = 'database';       // phpBB MySQL Database Name.
    
    $wgAuth_Config['UserTB']         = 'phpbb3_users';       // Name of your phpBB user table. (i.e. phpbb_users)
    $wgAuth_Config['GroupsTB']       = 'phpbb3_groups';      // Name of your phpBB groups table. (i.e. phpbb_groups)
    $wgAuth_Config['User_GroupTB']   = 'phpbb3_user_group';  // Name of your phpBB user_group table. (i.e. phpbb_user_group)
    $wgAuth_Config['PathToPHPBB']    = '../phpbb3/';         // Path from this file to your phpBB install.
    $wgAuth_Config['URLToPHPBB']     = 'http://www.domain.com/phpbb3/'; // URL of your phpBB install.
    
    $wgAuth_Config['UseWikiProfile']   = false;   // Whether the extension checks for a custom username profile
                                                  // field in phpBB when the phpBB username is incompatible with
                                                  // MediaWiki username restrictions.

    $wgAuth_Config['ProfileDataTB']    = 'phpbb3_profile_fields_data';  // Name of your phpBB profile data table. (e.g. phpbb_profile_fields_data)
    
    $wgAuth_Config['ProfileFieldName'] = 'pf_wikiusername';             // Name of your phpBB custom profile field
                                                                        // The 'pf_' is always prefixed to the custom field name you choose.
                                                                        // e.g., "wikiusername" becomes "pf_wikiusername"
    
    // Local
    $wgAuth_Config['LoginMessage']   = '<b>Please register on the forums to login.</b><br /><a href="' . $wgAuth_Config['URLToPHPBB'] .
                                       'ucp.php?mode=register">Click here to create an account.</a>'; // Localize this message.
    $wgAuth_Config['NoWikiError']    = 'You must be a member of the required forum group.'; // Localize this message.
    
    $wgAuth = new Auth_phpBB($wgAuth_Config);     // Auth_phpBB Plugin.
