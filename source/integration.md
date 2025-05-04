ILS Integration
---------------------------------

### About ILS Integration

Currently only Koha is implemented, but interactions with other ILSes could be added in the future.

#### Koha

##### Features
Vendors can be synchronized between Coral and Koha.

##### Prerequisites

Koha 18.05 or later is needed to use OAuth authentication. Earlier versions of Koha used cookie-based authentication to access the REST API, which is not suitable in our case.

##### Installation
In Coral, install Unirest and league/oauth2-client in resources/api_client (composer might be the easiest way to do it)

In Koha, install Net::OAuth2::AuthorizationServer and enable RESTOAuth2ClientCredentials system preference.

##### Configuration
In Koha, create a user for the REST API, edit this user, click on more -> manage API Keys -> Generate new client id/secret pair.

Also, click on More -> Set permissions and give your new user the `vendors_manage` permission (found under the Acquisition management section).

In Coral, edit the `[ils]` section in `organizations/admin/configuration.ini`, and add/edit the following values:

```
[ils]
ilsConnector=koha
ilsVendorRole="Vendor"
ilsApiUrl="http://mykoha.tld/api/"
ilsAdminUrl="http://pro.mykoha.tld/"
oauthid="<paste Client ID generated in Koha>"
oauthsecret="<paste Secret generated in Koha>"
```

- `ilsConnector` is the name of the ILS you want to connect to.
- `ilsVendorRole` is the Coral organization role you want to map with Koha vendors.
- `ilsApiUrl` is Koha's API address.
- `ilsAdminUrl` is Koha's intranet address.
- `oauthid` is the client ID generated in Koha.
- `oauthsecret` is the secret generated in Koha.

Once enabled, you can search for an existing vendor in Koha when creating an organization in Coral:

![Vendor does not exist in koha](img/integration/integrationVendorCheck.png)

If the vendor does not already exist in Koha, it will be added if the vendor role has been selected.

If the vendor already exists in Koha, we can automatically retrieve its informations:

![Vendor exists in koha](img/integration/integrationVendorExists.png)
![Vendor is retrieved from koha](img/integration/integrationVendorRetrieve.png)

In both cases, the vendor can no longer be modified in Coral, as the ILS is authoritative.

Once the Coral organization and the Koha vendor are linked, a link to open the vendor in Koha is displayed:
![Vendor in Coral](img/integration/integrationVendorDisplay.png)

