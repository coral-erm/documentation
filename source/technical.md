Technical Documentation
------------------------

### Supported Browsers 
* Current and recent Firefox, Microsoft Internet Explorer, Microsoft Edge, Safari, Seamonkey, Opera, and Chrome versions are all supported 
* Requires Browser to have Javascript enabled – the CORAL interface relies heavily on AJAX, JQuery and JQuery plugins 

### Server-Side Requirements and Setup 

#### Database
 
CORAL runs with a MySQL 5 backend.  Each CORAL module uses its own MySQL schema.  The recommended format for schema name is `coral_MODULENAME_prod`, e.g. `coral_resources_prod`.  Since the database name is set in the `configuration.ini` file this name can be easily changed. For security purposes it is recommended to have a “coral” user which has select, insert, update and 
delete privileges only (no create table privileges).  This “coral” user should have access to all CORAL databases for interoperability. 

The CORAL modules are heavily data-driven.  As a result we have provided an initial “snapshot” of our 
data which will be inserted when the MySQL tables are created.  Most of this data may be edited 
through the front-end if desired – refer to the user guide for more information. 
For data models and the MySQL Workbench file, refer to [http://docs.coral-erm.org/](http://docs.coral-erm.org/)  

#### Server-side scripting 

A [supported version of PHP 5](http://php.net/supported-versions.php) is a requirement for CORAL since it uses Object Oriented code.  If you are unsure whether you’re
running PHP 5, be sure to install using the web installation script since it will validate your version.  See 
Installation section, below. Please note, a minimum version of PHP 5.5 is supported.

#### Authentication 
Authentication is required for CORAL since all users are given specific privilege levels (for more on
privilege levels refer to the user guide). CORAL has two choices for authentication – either to tie in to
your university/library’s authentication system or to install the CORAL Authentication Module. The Authentication Module also has built in LDAP functionality. For more
information on the CORAL Authentication module, please refer to the [Authentication Documentation](http://docs.coral-erm.org/en/2.0.1/authentication.html).

To utilize your library’s existing authentication system, you will need to know the Server Variable name
to use within PHP. This is a setting within configuration.ini called “remoteAuthVariableName” which will
be set in the installation process (see Installation, below). The remoteAuthVariableName indicates the
PHP server variable name that is set in Apache upon authentication and so CORAL will recognize the
user's login. Different server setups may have different variable names. Assuming you're using an
authentication system already, you can do a phpinfo script to see if this variable is being set and what it is. (just make a .php file and put `<?php phpinfo(); ?>` in it ) Probably the easiest way to find the variable
name would be to search for your user ID.

If you still have problems using your existing authentication system, we would recommend subscribing
to the listserv and looking at the archives to see how other institutions have made it work, or searching the forum.
As a last resort, each module has ‘user.php’ in the root directory which can be modified to retrieve your
users’ login ID.

Also, the `.htaccess` file should require your authentication system, or you should have another way to
require authentication. If you have questions about this, contact your system administrator.

CORAL has limited LDAP username lookup available in most modules. This is different than the more complete LDAP integration present in the Auth module. Within each of the modules (except for Licensing) any user
allowed by the `.htaccess` may visit the site; if LDAP has been set up in the `configuration.ini` it will display
their name in the upper right hand corner. This connection is optional.


### Application Architecture 
CORAL is written using object oriented code.  All classes and configuration are defined under the 
`/admin/` directory.  The admin directory has an `.htaccess` file in order to prevent web users from 
accessing config data. 

`/directory.php` is used to load all of the classes.  This is called from every page and for the ajax functions 
and also contains some globally used functions. 

`/admin/configuration.ini` defines config settings (module settings, database and ldap) 

Common classes are in `/admin/classes/common/` - includes configuration, database connection, LDAP, 
Utility, Email 

#### Database 
All database tables have matching classes and all database SQL code is done within methods of these 
classes. 

Every class extends the DatabaseObject for generic  SQL handling (retrieving all values, inserts, updates, 
deletes).  DatabaseObject is in `/admin/classes/common/` and the database classes are located in 
`/admin/classes/domain/` 

#### Web side 
Pages - `admin.php`, `index.php`, detail view scripts and reports are all shells for positioning divs.  Within 
each div data is updated via ajax calling `ajax_htmldata.php`.  This way when changes are made to the 
data the display can be immediately updated without needing to refresh the entire page. 

A jquery plugin called thickbox is used to display the “popup” forms with the grayed out background. 

`ajax_forms.php` contains all of the html for forms that are being used by thickbox. 

`ajax_processing.php` contains all of the processing (eventually sql insert/updates, etc) that utilize the 
database classes - this is where data is sent after being entered in the forms 

`header.php` and `footer.php` are defined within /templates/ directory. 

`user.php` and `setuser.php` are used for authentication. 

`/css/styles.css` is the general css file used by all pages in coral. 

#### Javascript 
All javascript code is stored under `/js/` directory.  This directory contains javascript for base pages
(`index.php`, `admin.php`) where the names of files correspond with the javascript file name. (e.g. 
`admin.php` uses `admin.js`) 

The directory `/js/plugins/` directory contains third party javascript (jquery library, autocomplete, 
thickbox and other plugins) . 

The `/js/forms/` directory contains all javascript files corresponding to the thickbox forms that are 
generated from `ajax_forms.php` 

#### Naming standards 
If you would like to contribute code / database changes to the project it is recommended that you stay
within the following naming conventions. 

##### Database Naming:
 
* Attribute and entity names in singular form and using present tense 
* Entity names are uppercased with no spaces or underscores between words (camel case) 
* Attribute names have first word lower cased, subsequent words have first letter upper case 
    * e.g. documentID, sentDate, documentText 
* Primary keys are always Auto Incrementing Integers beginning with the table name and ‘ID’ 
suffix 
    * e.g. for the “Document” table, “documentID” is the name of the primary key 
    * (note that the generic database object expects this naming convention – if you do not use this name, you must override the primary key name in the class) 
* When possible, attribute names are suffixed with class words 
    * Code - Has a list of possible values e.g. INV, ENC 
    * ShortName - Name 
    * Identifier (ID)  - Unique Identifier, generally surrogate key 
    * Number - Numeric value 
    * Indicator (Ind) - A Boolean value (0/1)
    * Date - Date 
    * DateTime - Date and time stamp 

##### Code: 
* Refer to previous sections for directory structure, script locations and script naming. 
* Variable, class and attribute names mimic the database standards with camel case. 
* Method names are prefixed with:  
    * Is – Answers a question, returns true/false, e.g. for User class `isAdmin()` method 
    * Get – retrieves a value 
    * Set – sets a value

#### Database Schema

For an overview of the database schema see [CORAL Database Schema](http://www.coral-erm.org/schema/) 