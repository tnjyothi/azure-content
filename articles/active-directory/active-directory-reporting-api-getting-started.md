<properties
   pageTitle="Getting started with the Azure AD Reporting API | Microsoft Azure"
   description="How to get started with the Azure Active Directory Reporting API"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/07/2016"
   ms.author="dhanyahk"/>


# Getting started with the Azure Active Directory Reporting API

*This documentation is part of the [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Azure Active Directory (AD) provides a variety of activity, security and audit reports. This data can be consumed through the Azure classic portal, but can also be very useful in a many other applications, such as SIEM systems, audit, and business intelligence tools.

The [Azure AD Reporting APIs](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) provide programmatic access to these data through a set of REST-based APIs that can be called from a variety programming languages and tools.

This article will walk you through the process of calling the Azure AD Reporting APIs using PowerShell. You can modify the sample PowerShell script to access data from any of the available reports in JSON, XML or text format, as your scenario requires.

To use this sample, you will need an [Azure Active Directory](active-directory-whatis.md) tenant.

## Creating an Azure AD application to access the API

The Reporting API uses [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) to authorize access to the web APIs. To access information from your directory, you must create an application in your Azure AD tenant, and grant it appropriate permissions to access the Azure AD data.


### Create an application
- Navigate to the [Azure classic portal](https://manage.windowsazure.com/).
- Navigate into your Azure AD tenant.
- Navigate to the **Applications** tab.
- On the bottom bar, click **Add**.
	- Click "Add an application my organization is developing".
	- **Name**: Any name is fine. Something like "Reporting API Application" is recommended.
	- **Type**: Select "Web application and/or Web API".
	- Click the arrow to move to the next page.
	- **Sign-on URL**: ```http://localhost```.
	- **App ID URI**: ```http://localhost```.
	- Click the check mark to finish adding the application.

### Grant your application permission to use the API
- Navigate to the **Applications** tab.
- Navigate to your newly created application.
- Click the **Configure** tab.
- In the "Permissions to Other Applications" section:
	- In the Azure Active Directory > Application Permissions, select **Read directory data**.
- Click **Save** on the bottom bar.


### Get your directory ID, client ID, and client secret

The steps below will walk you through obtaining your application's client ID and client secret.  You will also need to know your tenant name, it can be either your *.onmicrosoft.com or a custom domain name.  Copy these into a separate place; you'll use them to modify the script.

#### Application Client ID
- Navigate to the **Applications** tab.
- Navigate to your newly created application.
- Navigate to the **Configure** tab.
- Your application's client ID is listed on the **Client ID** field.

#### Application client secret
- Navigate to the **Applications** tab.
- Navigate to your newly created application.
- Navigate to the **Configure** tab.
- Generate a new secret key for your application by selecting a duration in the "Keys" section.
- The key will be displayed upon saving. Make sure to copy it and paste it into a safe location, because there is no way to retrieve it later.


## Modify the script
Edit one of the scripts below to work with your directory by replacing $ClientID, $ClientSecret and $tenantdomain with the correct values from “Delegating Access in Azure AD”.

### PowerShell Script

    # This script will require the Web Application and permissions setup in Azure Active Directory
$ClientID      = "890a8fdf-ff33-4160-8214-b69d5b9a7fef"            # Should be a ~35 character string insert your info here
$ClientSecret  = "X7o859BmQ5aNJXxxArH4X+R+Dw02SEM7ZLL82xCsalw="          # Should be a ~44 character string insert your info here
$loginURL      = "https://login.windows.net"
$tenantdomain  = "b2ctesting.onmicrosoft.com"            # For example, contoso.onmicrosoft.com
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
# Returns a list of all the available reports
    Write-host List of available reports
    Write-host =========================
    $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
    Write-host $allReports.Content
Write-host
    Write-host Data from the b2cUserJourneySummaryEvents report
    Write-host ====================================================
    Write-host
    # Returns a JSON document for the "b2cUserJourneySummaryEvents" report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cUserJourneySummaryEvents?api-version=beta")
    Write-host $myReport.Content
    Write-host
    Write-host Data from the b2cUserJourneyEvents report with datetime filter
    Write-host ====================================================
    Write-host
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cUserJourneyEvents?%24filter=TransactionEnd+gt+2015-08-20+and+TransactionEnd+lt+2015-08-22+and+ObjectId+eq+'7e262b56-993d-4699-80b7-83f1f71d4558'&api-version=beta")
    Write-host $myReport.Content
# Options for other output formats
# to output the JSON use following line
    $myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force
# to output the content to a name value list
    ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath b2cUserJourneySummaryEvents.txt -Force
# to output the content in XML use the following line
    (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath b2cUserJourneySummaryEvents.xml -Force
} else {
    Write-Host "ERROR: No Access Token"
    }


### Bash Script

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Execute the script
Once you finish editing the script, run it and verify that the expected data from the AuditEvents report is returned.

The script lists all the available reports, and returns output from the AccountProvisioningEvents report in the PowerShell window in JSON format. It also creates files with the same output in JSON, text and XML. You can experiment by modifying the script to return data from other reports, and comment out the output formats that you do not need.

## Notes

- There is no limit on the number of events returned by the Azure AD Reporting API (using OData pagination).
- For retention limits on reporting data, check out [Reporting Retention Policies](active-directory-reporting-retention.md).


## Next Steps
- Curious about which security, audit, and activity reports are available? Check out [Azure AD Security, Audit, and Activity Reports](active-directory-view-access-usage-reports.md)
- See [Azure AD Audit Report Events](active-directory-reporting-audit-events.md) for more details on the Audit Report
- See [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) for more details on the Azure AD Graph API REST service
