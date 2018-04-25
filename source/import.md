Import Tool
-----------

This tool allows to import resources and organizations from a CSV file.

#### Configuration

Change the admin/configuration.ini file to specify on which input columns should the IsbnOrIssn be deduped for the import tool:

[settings]

importISBNDedupingColumns="ISSN,EISSN,PrintISSN,OnlineISSN,Print ISSN,Online ISSN,ISBN,EISBN"
For a resource in the csv file, if there is a value in one of these columns that match an existing isbnOrIssn, the resource won't be imported.

#### CSV Format

The first line of the CSV file must be column headers, not data.

Example: titleText,resourceURL,ISSN,EISSN,organization,role,parentResource

Then, each line is a resource.

Use

First screen

The user must choose the file to upload and the CSV separator (comma, semi-column or pipe).

Second screen

The user must choose which columns of the CSV file are to be used for import. If a column header name matches a database column name, it will be automatically selected accordingly.

Third screen

It's a report of:

the number of resources processed
the number of resources imported
the number of parents imported
the number of resources that have been attached to an existing parent
the number of organizations created
the number of resources that have been attached to an existing organization.

#### Resources import rules

Resources are only created. If a resource already exists in Coral, it will not be updated

#### Organizations import rules

If an organization is specified for a given resource, the import tool will check if the organization already exists. If not, it will be created. In both cases, the resource will be attached to it.
Organizations import works whether the organization module is used or not.
If multiple organizations with the same name exists in Coral, the import tool will not attach the resource, and a warning will be issued.
A role can be specified.

#### Parent resources import rules

If a parent resource is specified for a given resource, the import tool will check if the parent resource already exists. If not, it will be created. In both cases, the resource will be attached to it.

#### Pending (pull request #57)

##### Resource import

Improvement: When a resource is imported, it is created if it doesn't already exist with the same parent. So a resource can be duplicated.

##### Import Tool

Import tool is common for all types of import (csv and GOKb for now).

To use it, you need to operate a pretreatment on your data to call ImportTool->addResource($datas, $identifiers).

$datas must be an array in which keys are resource attributes names and values are corresponding text, except for organizations, aliases, parent and resource type (see following section).

$identifiers must be an array in which keys are the identifier type (optional), if there is no key, identifiers will be treated as ISxN.

##### Datas array:

All resource object attributes can be considered as key in this array and values as the content of this attributes, except organization and aliases. example : $datas['titleText'] = "name of the resource";

Organizations and aliases treatment are pretty different: $datas['organization'] and $datas['alias'] are arrays:

**Organizations**:

This array has to be like that: 'role' => "org name"

example: $datas['organization'] = array [ 'provider' => "provider name" , 'platform' => "platform name"];

**Aliases**:

This array has to be like that: 'type of alias' => array ["name"]

example: $datas['alias'] = array [ 'alternateName' => array [ "alternate name 1" , "alternate name 2" ], 'nameChange' => array [ "old name 1" , "old name 2" ] ]

**Parent resource**:

resource->parentResource doesn't exist as an attribute but, if there is a parent, you must add it in $datas like this : $datas['parentResource'] = "parent name";

**Resource type**:

Same as parent, resource->resourceType doesn't exist as an attribute but you must add it in $datas like this : $datas['resourceType'] = "type of the resource";
