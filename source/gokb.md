GOKb Integration
----------------

Coral is linked with GOKb (Global Open Knowledge Base) which groups a lot of e-resource metadatas.

Prerequisite

upgrade your database with the file: update_gokb.sql
install php oai-pmh on your server : https://github.com/caseyamcl/phpoaipmh
install history.js for your browser: https://github.com/browserstate/history.js
Search tool

You can search resource by name, organization or ISxN. A search by organization or IsxN will only return titles (not packages).

To search, you have to open the "New resource" form and fill at least one search field. Then, click on 'Search on GOKb' button or on a magnifying glass icon. When results are displayed, you can consult resource's details and TIPPs (titles included in package, or package in which the title is included in) thanks to 'Details' button. You can import the resource by clicking on 'Select' button from results or details screen.

/!\ For now, search request are proceed on a test database; GOKb team is working on it and the DB should become a prod one at the end of the year 2015. /!\

Import tool

You can import resource from your search results by clicking on 'Select' button. When you decide to import a package, all the titles included in are imported too but you can personalize package content (see following section).

Multiple approach has been adopted, ie. a title can be duplicated. For example, if the title "Coral is great" is already in your database and you decide to import the package "Best tools for libraries" in which this title is included. Then, import tool will check if the existing title has the same parent, if not "Coral is great" will be imported. So you will have this resource twice and you can attach different datas to each one.

A resource imported from GOKb will have, at most, the following informations:

Name

Acquisition type°

Organization

Aliases

Parent resource*

Format*

URL*

Coverage*

gokb identifier

ISxN*

Resource's identifier of provider*

(*) for titles only (°) for packages only

Package content customization

You can personalize package content once it is imported. You can also custom package you already got even if it was not imported from GOKb. The "custom screen" display the current package's children. If the checkbox is checked: the resource will be kept, else it will be removed from database when you will click on 'Confirm customization' button.

This is a test!!!
