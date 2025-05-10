## Centralizing Server Storage with Azure File Sync

***
In this article, I'll be demonstrating how to centalize/extend your on-prem server storage with Azure File Sync.

To perform this operation, we need a sync group; what does that mean? In simple terms, it is a container that houses participating resources of a sync operation.

[sync group illustration]

A sync group consists of 2 components: cloud endpoint and server endpoint

- **Cloud endpoint**: A cloud endpoint is an azure files service residing in a storage account. You can only have one cloud endpoint per sync group.
- **Server endpoint**: A server endpoint is the participating on-prem windows server that is being synced. You can have as many server endpoints as you want in a single sync group.

Now that you have an understanding of the terms, lets begin

Navigate to the [Azure portal](portal.azure.com), on the search bar type _sync_ and select storage sync services from the drop down.

[screenshot 1]

Click create to deploy our Azure File Sync

[screenshot 2]

Specify the resource group, location and name for your storage sync service. For the purpose of this demo i'm choosing _serverSync_ as the name and _east US_ for location

_NB: The region you choose your storage sync services to be located should match the region of the storage account you intend to use._ 

[screenshot 3]

Click next to Networking tab, select "All networks"

[screenshot 4]

Click "review and create", verify the information then "create" to deploy the file sync service

[screenshot 5]

[screenshot 6]

Next, we'll create the Azure Files File share to use as our cloud enpoint. Navigate to the storage account.

In the storage blade, expand "Data storage" and click on "File shares"

[screenshot 7]

Click "+ File share" to create a new file share, choose a name for it and leave other things as default. Click "review + create" then "create"

[screenshot 8]

Now we can see our newly created file share

[screenshot 9]

Next, we'll create our sync group; Navigate to the "serverSync" Storage sync services we created earlier.

In the left blade, expand "sync" and select "sync groups" or from the overview screen on properties tab as highlighted below

[screenshot 10]

I currently have no sync groups, so let's create one. Click on the "+ Create a sync group"

[screenshot 11]

Give the sync group a name of your choice, using "sync-group1" for this demo.
To specify our cloud endpoint, select the subscription where the storage account is located, then select the storage account with Azure Files in it. The Azure file share should automatically be selected after. 

[screenshot 12]

_NB: if you don't see any storage account listed when "Select storage account" is clicked, then it isn't in the choosen region._

[screenshot 13]

Continue to create, you should see two notification; one creating the sync group and another for the cloud endpoint.

Once done, we can see our sync group ready to go.

[screenshot 14]

Next, we have to configure our server endpoint but before that; we have to register it.

In the sync tab beneath "sync groups", click on "registered servers"

[screenshot 15]

As we can see, its empty. Click on the link to download the Azure File Sync agent for your intended server

[screenshot 16]

For this demo, I'm downloading the StorageSyncAgent_WS2019.msi because I am registering a Windows Server 2019

[screenshot 17]

Let's go ahead and install our file sync agent

[screenshot syncagent]

Once the installation complete, launch the application and follow the prompt. Select "AzureCloud" in the Azure environment then click "sign in"

[screenshot server registration]

After authenticating, then we can choose the storage sync service where this server will be registered, which in our case is the "serverSync" then click "register"

[screenshot server registration 2]

If registration was successful, you'll get this screen.

[screenshot server registration 3]

NB: The connectivity test could take a minute or two depending on your connection strength.

On the Azure portal, navigate to the sync group created earlier and click "+ Add server endpoint"

[screenshot 18]

Select the on-prem server we just registered from the drop down, in my case "SRV-DC1.sixth-sensei.local". Next choose the path where the sync will be initiated from i.e all files in this path will be synced.

[screenshot 19]

You can also enable **cloud tiering**, this allows you to manage your local storage more efficiently by storing only frequently accessed files on your local server while infrequently accessed data still have a namespace on the server but content are retrieved from Azure File share when needed.

_Note:_

- Cloud tiering cannot be enabled on system volume partition (C:). 
- If the specified path does not exist, it is automatically created

Click on "create" to add the server endpoint

We can see our server is now added to the sync group and ready to sync

[screenshot 20]

Also on the server itself, we can see the path created as it didn't exist

[screenshot server path empty]

Let's verify the setup by copying a file to the sync path

[screenshot server path populated]

Go to the server-sync file share > Browse to verify the file exists

[screenshot 21]

Voila! 

Any file uploaded to the file share directly also gets replicated to the on-prem server completing a two-way sync and ensuring high availability.

