Installing CORAL Manually
-------------------------

### Notes for Installing CORAL 3.0 Manually

When you first [download the code](https://github.com/Coral-erm/Coral/releases), you will want to place the unzipped 3.0 code in your web server directory. 

You will need to configure each module individually. 

Depending on whether the module allows you to upload attachments or documents you may also need 
to `chmod 777` some directories:  
For Licensing – both `/licensing/attachments/` and `/licensing/documents/` directories  
For Usage Statistics – both `/usage/archive/` and `/usage/log/` directories  
For Resources – `/resources/attachments/`  

#### Getting Started
 
**Install database tables**

Step 1: Create new database schema (recommended name is `coral_modulename_prod`)

Step 2: Apply the SQL file to your new database. These can typically be found in the /module/install/ directory.

Step 3: Manually add an admin user into User table – this is required for the first person to administer users. If you are going to use the auth module you will need to add the admin user there as well as each other module's User table. 

- Open your MySQL client
- Navigate to your database (`coral_modulename_prod`) and to the User table
- Insert  your LoginID (either the one you've already added to the Authentication Module, or your externally authenticated login ID) with a  Privilege ID of 1 (equates to admin) and your first and last name, if desired.
- This is the user who will have access to the Admin page to first administer other users.  From the Admin Page, you can designate another user with the admin privilege for that person to also administer users.

**Update Configuration.ini files**
For each module follow these steps.
Step 1: Rename `/admin/configuration_sample.ini` to `/admin/configuration.ini`

Step 2: Edit the `/admin/configuration.ini` file under `[settings]`

**Interoperability Flags**
- `organizationsModule=`
- `authModule=`
- `licensingModule=`
- `resourcesModule=`
- `usageModule=`
  
These switches define whether you have the other CORAL modules installed and would like the interoperability turned on.  The switch will also turn on/off the link to other modules (the Change Module drop down menu in the upper right-hand corner).  Valid values are Y or N.  Interoperability currently occurs between:
 

- Resources to Licensing and Organizations 
- Organizations and Licensing (both directions) 
- Usage Statistics to Organizations

**Interoperability Database Names**
- `organizationsDatabaseName=`
- `licensingDatabaseName=`
- etc.

When you have interoperability turned on between Organizations and Licensing database or Organizations 
and Usage Statistics you must supply the database name (e.g. `coral_organizations_prod`) for the connection.  Also note that the user you connect with must have select, insert, 
update and delete privileges to this database. 
  
- [resources only] `defaultCurrency=`

This will set the currency that is selected by default in the Resource Payment section.  Default currency needs to be a valid Code in the Currency tab on the Admin page.
  
- [resources only] `enableAlerts=`

This will turn on the alerting checkbox on the Acquisitions tab on the Resource page for subscription end dates.  The days in advance 
and email addresses can be set in the Alert Settings on the Admin page.  Also note this requires a scheduled job, see below on Resources Alerts Scheduling.
 
- [resources only] `catalogURL=`

If you would like to include a link directly into your catalog 
from the Resource page you can place your URL with the parameter for the System Number at the very end.  For example, ours is: 
"http://alephprod.library.nd.edu/F/?func=direct&doc_number="
 
-[resources only] `feedbackEmailAddress=`

This is the global email address for all 
workflow notifications - starting, queue entries and completion.  Emails for specific steps can be set in the User Group section on Workflow/User Group tab of the Admin page.  The feedback email address also shows on the Resource Right Panel.  It is optional.
 
- [licensing only] `useSFXTermsToolFunctionality=`

This will turn on/off the SFX tab and 
Terms Tool Report in the licensing module.  Valid values are Y/N.
  
- [usage only] `reportingModule=`

This will turn on/off the reporting link in the usage 
statistics module.  Set to Y if you are using the reporting module add-on (this is a separate install).  Valid values are Y/N.

- [usage only] `useOutliers=`  

This will turn on/off the outlier flagging feature.  This feature 
will calculate abnormal spikes in usage and color code them red, orange or yellow based on the formula defined in the admin tab.  Valid values are Y/N.
  
-[usage only] `baseURL=`

This will turn on or off a created link within the module to your open URL.
 
- `remoteAuthVariableName=`

This is the server variable used by php to determine the 
user authorized to log in.  For example, we use `$HTTP_SERVER_VARS['REMOTE_USER']`, `$_SERVER['PHP_AUTH_USER']`, or `$_SERVER['WEBAUTH_USER']` may be other names.

- [resources only] `defaultsort=  `

Changes the default sort order for the resources.  Example: defaultsort=\"TRIM(LEADING 'THE ' FROM UPPER(R.titleText)) asc\"  It is optional.

Step 3: Under `[database] `

- `type=mysql  `
currently only MySQL is supported but other database types could be supported if modifications to the generic database classes are made
 
- `host=  `
MySQL server could be localhost as well
 
- `name=  `
Database name (e.g. coral_resources_prod)
 
- `username=  `
recommended to have a single user with select, update, insert, delete 
access to all CORAL modules 

- `password=  `
password for above mentioned user
 
Step 4:  Under \[ldap] (optional – not for Licensing Module or Usage Statistics Module) 
    1. Host=  
server name 
    1. Search key =  
filter parameter used in `ldap_search` call – for more info refer to 
http://php.net/manual/en/function.ldap-search.php 
    1. Base DN=  
The Base DN for the directory(`base_dn` parameter used in `ldap_search` call) 
    1. First Name Field=  
Name of the field that LDAP uses for first name – for us it’s 
“givenname” 
    1. Last Name Field=  
Name of the field that LDAP uses for last name – for us it’s “sn” 
1. For security reasons, remove the `/install/` directory once the installation is complete
1. If you are using an external authentication system, you will need to require it in the `/coral/[module_name]/.htaccess` file
1. If you are installing more than one module for interoperability you may see errors from either 
side until both modules are installed.
1. Join the listserv used for product updates, support, and general discussion by sending an email to listserv@listserv.nd.edu. Leave the subject line blank and include 'SUBSCRIBE CORAL-ERM Your Name' in the body; ex. SUBSCRIBE CORAL-ERM John Smith.


**CORAL 3.0 Configuration.ini**
There is one last configuration.ini file that you'll need to edit. In the root coral directory you will see a /common/ directory. Inside is a `configuration_sample.ini` file which you will want to copy or rename to `configuration.ini`

- `[installation_details]` has a `version = ` which should be set to `"3.0.0"`
- `[database]` has the same values that you entered for that section in the specific modules
- each module has it's own entry like `[licensing]` with `enabled=` and `installed=` flags which can be set to `"Y"` or `"N"` 


## Additional Server Configuration
### Resources Alerts Scheduling 
The Resources module includes a script called `sendAlerts.php` that can be run nightly to send out 
subscription alerts. To set it up via cron job, you will need to invoke php. You need to access the alerts page via curl or wget in order to generate emails with functional links:

`0 0 * * * /usr/bin/wget http://YOUR_PATH…/coral/resources/sendAlerts.php`

You will also need to verify that enableAlerts is set to Y in `/resources/admin/configuration.ini`

Note that this will send an email to the feedbackEmailAddress also set in `configuration.ini`.  For 
temporary testing purposes you may wish to change or remove this email address.  The number of days 
in advance and other email addresses can be modified in the Alert Settings tab of the Admin page. 

By default CORAL will open modules in new windows. However, it can be configured to open modules in the same window.

In common/configuration.ini:

[settings]
open_new_windows = Y/N


### Additional CORAL Resources customization options 

#### Payment formatting 
To change the format of the display of amounts (e.g. use a comma in the place of a period), you can 
modify the `/resources/directory.php` file, `integer_to_cost()` function.  The `number_format` function is a
php function – refer to PHP documentation for more information: 

http://php.net/manual/en/function.number-format.php

#### Date formatting 
To change the format of the date from the US standard MM/DD/YYYY you can modify the 
`/resources/directory.php` file, `format_date()` function.  The `date()` function is a php function – refer to 
PHP documentation for more information: 

http://php.net/manual/en/function.date.php

The date will need to be convertible by the php `strtotime` function.  For European dates the `strtotime` 
function requires dashes be used instead of slashes.  Also for the year, be sure to use Y (uppercase) for 4 
digit year and y (lowercase) for 2 digit year. 

Additionally for date formatting you will need to update a javascript file for the date picker calendar 
functionality.  Be sure this format matches the format you set in `directory.php`! 
`/resources/js/common.js`, line 35: 

`Date.format = 'mm/dd/yyyy';`

or 

`Date.format = 'dd-mm-yyyy';`

### Number formatting
CORAL can be configured to define how numbers should be parsed and displayed. The default format is:

    - en_US
    - 2 decimals

To define how numbers display in your instance, install the php-intl package.

The configuration takes place in common/configuration.ini, as such:

[settings]
number_locale = "fr_FR"
number_decimals = 2

If number_locale is omitted, it will be defaulted to en_US
If number_decimals is omitted, it will be defaulted to 2

This will affect every part of the resources module using
cost_to_integer and integer_to_cost function:

    new resource, api, api_client, imports, summary and cost history.

Special sql processing has been made to exports and dashboards.
Special javascript processing has been made to cost history.

#### Email customization 
The templates for the workflow notification and alert emails are located in `/admin/emails/`

You can modify the text in these as needed, leaving the words inside brackets `< >` as is – you can change
where they appear but leave the format alone. 

### Upgrades 
Upgrades to CORAL will be announced through the CORAL ERM Listserv.  If you wish to be added, please 
consult https://coral-erm.org or email coral-admin@listserv.nd.edu directly. 

In general, small upgrades with simple coding changes will not be given their own version numbers.  If
an institution notifies us about a bug we will let them know directly when the bug fix is available.  
Versioning will happen when there is a database structure change or configuration file change. 

When performing upgrades your configuration file (`/admin/configuration.ini`) should always be backed 
up first so that when you check out the new code you are sure to not overwrite it. 

After you have checked out the new code through GitHub, there will be a file called `UPGRADE_README` in 
the install directory which will detail the database changes and configuration changes incorporated in
that version change. 

As with the Installation, you have two choices for how to upgrade – either manually or web install.   

For the web install, go to `/install/update.php` and follow the instructions on the screen.  

For the manual install, refer to the `UPGRADE_README` file in the install directory for instructions on 
how to upgrade – instructions may be different depending on what types of changes are included with 
the upgrade. 

**After performing upgrade, be sure to remove your `/install/` directory.**

### New to PHP? 
If your institution is new to the PHP/MySQL setup, installation may be a bit more tricky. 

First off, make sure that you’re both installing php with the `-with-mysql` option and installing the phpmysql package.  To verify that php and mysql are installed and working correctly, you can make a test 
php script with only the following: 

`test.php` contents:  

`<?php phpinfo(); ?>`

To debug issues with authentication you can override authentication in the 
`/coral/modulename/user.php` script. 

You may also wish to change the error reporting level in `php.ini` if you are getting blank pages in the
install or with the CORAL pages themselves. 

### Possible issues during installation (or - things to verify if CORAL is not working at this point) 
Verify you’re running PHP 5 

Verify your database user can connect to the MySQL database and that all tables were created properly 

If your login isn’t working, verify that you have required your authentication service in the `.htaccess` file.  
You should be prompted with your institutions default login screen before getting to the CORAL screens. 

If you’re not sure what your remoteAuthVariableName, you can try the following: 
Create a file called test.php on your web server with the following line:  
`<?php phpinfo(); ?>`  
Then, bring this up in your web browser.  Search for your login ID and it should display the server 
variable in the lefthand column.

If interoperability isn’t working (you’ll know it’s not working when you get red errors), verify that you 
have the database setting and that your database user can connect to that database.  Also, be sure to complete installation of both modules involved.

If your remote user auth variable isn’t working, verify that you have `register_globals = On`


