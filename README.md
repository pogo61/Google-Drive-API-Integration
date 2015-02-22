# Google-Drive-API-Integration
API Integration is an API that combines the SalesForce and Google Drive API Hooks so that it takes a lead, inserts it into SalesForce, then Appends it to a file stored in Google Drive
## Google Drive API 
### About the API
- Access, edit, and save your Google App's files to Google Drive.
- API Documentation: [Google Drive API docs] (https://developers.google.com/drive/v2/reference/)

### Pre-Reqs
- This will only work for [Google Apps for work] (https://www.google.com/intx/en_au/work/apps/business/?utm_source=google&utm_medium=cpc&utm_campaign=japac-smb-apps-bkws-au-en&utm_content=gafb&utm_term=google%20apps&gclid=CPGWm82o5cMCFU06vAod9kcAOA&gclsrc=ds). You, or your organisation, must have a subscription to this service. The reason for this is that this is the only service that Google provides 2-legged OAuth to (via a service account). Google uses only 3-legged OAuth with its free and open Google Docs. 3-legged OAuth is unsuitable to server to server integration.
- Register the application in the [Google Developers Console] (https://console.developers.google.com/). Creating an account if you have not already registered.
- once the App is defined, double click on it to be taken the the App details page. 
- Click on the "Credentials" left hand menu item
- When the Credentials Portlet is displaid, click on the "Create new Client ID" button. then select the "Service Acccount" for the CLient ID type. (note: you must be the Google Apps sdministrator to do this).
- once this is done you should have a new section in the Credentials Portlet for the Service Account.
- Copy the clientId of the Service Account
- login to the  [Google Apps for work Admin Console] (https://admin.google.com) select the security icon, scroll to the botom of the admin page and select the "Show more" link, select the "Advanced Settings" link that appears, then the "Manage API client access" link.
- in the "Client Name" field paste the Service Account ClientId you coppied, then copy this list of API scopes into the Scopes field and click the "Authorize" button:
    https://spreadsheets.google.com/feeds,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/drive.appdata,https://www.googleapis.com/auth/drive.apps.readonly,https://www.googleapis.com/auth/drive.file,https://www.googleapis.com/auth/drive.metadata.readonly,https://www.googleapis.com/auth/drive.readonly
- Create an Account with [SmartyStreet] (https://smartystreets.com)
- Login, click on the "API Keys" left menu item to Obtain the Auth ID and Auth Token (they are under the "Secret Keys" section)

### Getting Started Instructions
#### Download and Import
- Download migration.properties
- Login to PolicyManager  example: http://localhost:9900
- Select the root "Registry" organisation and click on the "Import Package" from the Actions navigation window on the right side of the screen
  - click on button to browse for the GoogleDriveAPIHook.zip archive file 
  - make sure select the migrations.properties file 
  - click Okay to start the importation of the hook.
- this will create a "Google Drive API Integration" Organisation with the requisite artefacts needed to run the API.
- Create a leads.txt file in the "/sm70/instances/nd "directory

#### Verify Import
- Expand the services folder in the Google Sheets API Hook you imported and find TrialLeads_API_Google VS

#### Activate Anonymous Contract
- Expand the contracts folder in the Google Drive API Integration you imported and find the "Anonymous" contract under the "Provided Contracts" folder
- click on the "Activate Contract" workflow activity in the righ-hand Activities portlet
- ensure that the status changes to "Workflow Is Completed"

#### Configure Security
- select the TrialLeads_API_Google VS
    - select the "Operations" tab, then the "POST /leads" operation, then the "Process" tab for the operation.
    - Double click on the "ConfigureSmartyStreet" script activity 
    - Put in your Auth ID value into the "newQueryString" after the "auth-id=" and Auth Token after the "auth-token="
    - save the script activity
    - select the save process icon
    - Go to Google Drive Hook -> Policies -> Operational Policies -> Insert JWT into Downstream request policy
    - Click "modify" in the XML Policy Tab. An XML Policy Content editor dialog will be displayed.
    - change the value of the tns:fileLocation element to be the location and name of the file containing the private key of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/)). Note that the location must be absolute, not relative.
    - change the value of the tns:serviceAccountEmail element to be the email address of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/).
    - change the value of the tns:userAccountEmail element to be the email address of the person who's spreadsheets the API is going to use (this must be a user in the Google Apps for work domain that the administrator has created).
    - save the changes
    - click on the "Activate Policy" workflow activity in the righ-hand Activities portlet
    - ensure that the status changes to "State: Active"
- Unzip the com.soa.pso.policy.JWTToken_1.0.0.zip file into the /sm70 directory of you ND installation(s). For Each ND:
    - restart your ND
    - loging to the ND Admin console
    - select the "Insert JWT as HTTP Header Policy Handler" Policy in the "Avaialble Features" tab and follow the Wizard installation instructions


#### Verify Connectivity
- Using curl -H "Content-Type: application/json" -d '{"FirstName":"Paul","LastName":"Pogo40","Company":"SOA","Street":"2962 Rosemary LN NE","City":"Rochester","State":"MN","PostalCode":"55906","Email":"paul40@soa.com"}' http://Win7-64-vm:9905/GoogleLeads/leads (Make sure you use a unique value for both the Email and LastName values)
- The correct response should be:
    {"FirstName":"Paul","LastName":"Pogo40","Company":"SOA","Street":"2962 Rosemary LN NE","City":"Rochester","State":"MN","PostalCode":"55906","Email":"paul40@soa.com","SalesForceId":"00Q60000017Ar19EAC","SignUpStatus":{"salesForceStatus":"Success","MailGunStatus":"Success","Box":"Success"}}
- Log in to your Google Apps for Work Account [Google Drive] (https://drive.google.com/drive/#my-drive)
- ensure that there is a "Lead" folder.
- open the "Lead" folder and ensure there is a "leads.txt" file
- Open that file, wait for the preview to generate the contents, and ensure that your lead posted in the curl request has been appended to the bottom of the file.

### Modify and Build
In the event you need to change the API Hook.   Here are the instructions to do so. 

### License
Put a link to an open source license
