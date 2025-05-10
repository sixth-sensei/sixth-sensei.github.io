## Centralizing Server Storage with Azure File Sync

***
In this article, I'll be demonstrating how to centalize/extend your on-prem server storage with Azure File Sync.

To perform this operation, we need a sync group; what does that mean? In simple terms, it is a container that houses participating resources of a sync operation.

![sync group](https://github.com/user-attachments/assets/c8b49e5e-f8b9-4eef-8fcf-4e898a47b9a2)

A sync group consists of 2 components: cloud endpoint and server endpoint

- **Cloud endpoint**: A cloud endpoint is an azure files service residing in a storage account. You can only have one cloud endpoint per sync group.
- **Server endpoint**: A server endpoint is the participating on-prem windows server that is being synced. You can have as many server endpoints as you want in a single sync group.

Now that you have an understanding of the terms, lets begin

Navigate to the [Azure portal](portal.azure.com), on the search bar type _sync_ and select storage sync services from the drop down.

![1](https://github.com/user-attachments/assets/2a5fcb19-c001-4465-ab56-3726425debef)

Click create to deploy our Azure File Sync

![2](https://github.com/user-attachments/assets/cd9e0046-64a3-490e-81d0-bca7fb3b9a5e)

Specify the resource group, location and name for your storage sync service. For the purpose of this demo i'm choosing _serverSync_ as the name and _east US_ for location

_NB: The region you choose your storage sync services to be located should match the region of the storage account you intend to use._ 

![3](https://github.com/user-attachments/assets/d552703f-66ff-42ce-b240-5892f51d4e27)

Click next to Networking tab, select "All networks"

![4](https://github.com/user-attachments/assets/783de8dd-c49d-4ba9-91a6-7c50aeafa29c)

Click "review and create", verify the information then "create" to deploy the file sync service

![5](https://github.com/user-attachments/assets/1f49faa1-4025-4a84-beb2-f356198a3ddc)

![6](https://github.com/user-attachments/assets/1bc0a52f-806b-46e2-89d2-d988d199073c)

Next, we'll create the Azure Files File share to use as our cloud enpoint. Navigate to the storage account.

In the storage blade, expand "Data storage" and click on "File shares"

![7](https://github.com/user-attachments/assets/66dce10b-b4a2-47ed-b117-ae4e8480f5d2)

Click "+ File share" to create a new file share, choose a name for it and leave other things as default. Click "review + create" then "create"

![8](https://github.com/user-attachments/assets/ed5a6835-16b4-4a1a-84cf-9b0e259e03e4)

Now we can see our newly created file share

![9](https://github.com/user-attachments/assets/590a3ed6-f3f1-45dd-bd26-0d6dcdeef53a)

Next, we'll create our sync group; Navigate to the "serverSync" Storage sync services we created earlier.

In the left blade, expand "sync" and select "sync groups" or from the overview screen on properties tab as highlighted below

![10](https://github.com/user-attachments/assets/dc6f1e3d-5882-4f0e-b33b-242180bc1706)

I currently have no sync groups, so let's create one. Click on the "+ Create a sync group"

![11](https://github.com/user-attachments/assets/f41b5fa0-c2ac-4b15-bbf5-85d9419b103a)

Give the sync group a name of your choice, using "sync-group1" for this demo.
To specify our cloud endpoint, select the subscription where the storage account is located, then select the storage account with Azure Files in it. The Azure file share should automatically be selected after. 

![12](https://github.com/user-attachments/assets/092f3c62-3f1c-4b34-a200-ac6bc490cf18)

_NB: if you don't see any storage account listed when "Select storage account" is clicked, then it isn't in the choosen region._

![13](https://github.com/user-attachments/assets/9e3eae74-334b-4d60-b505-5118191623ba)

Continue to create, you should see two notification; one creating the sync group and another for the cloud endpoint.

Once done, we can see our sync group ready to go.

![14](https://github.com/user-attachments/assets/880de2ee-9269-4976-b8fc-aed4718b5a76)

Next, we have to configure our server endpoint but before that; we have to register it.

In the sync tab beneath "sync groups", click on "registered servers"

![15](https://github.com/user-attachments/assets/896ad320-8ea5-46ac-a27e-374d5238d05b)

As we can see, its empty. Click on the link to download the Azure File Sync agent for your intended server

![16](https://github.com/user-attachments/assets/749a6fca-019d-4fe7-9dd9-ed6cb8b6cada)

For this demo, I'm downloading the StorageSyncAgent_WS2019.msi because I am registering a Windows Server 2019

![17](https://github.com/user-attachments/assets/55b5e3cd-51f1-4569-9bc5-dadfdde79e97)

Let's go ahead and install our file sync agent

![syncagent](https://github.com/user-attachments/assets/a8c38ff9-0622-4311-9961-095bbc670ce9)

Once the installation complete, launch the application and follow the prompt. Select "AzureCloud" in the Azure environment then click "sign in"

![server registration](https://github.com/user-attachments/assets/89be5c76-0b1f-49d4-ac64-12820bf18342)

After authenticating, then we can choose the storage sync service where this server will be registered, which in our case is the "serverSync" then click "register"

![server registration 2](https://github.com/user-attachments/assets/1434c05a-02e4-4606-bfa6-bfcbc1da4239)

If registration was successful, you'll get this screen.

![server registration 3](https://github.com/user-attachments/assets/0ef53c49-dacb-490a-8166-7fe13eb11ada)

NB: The connectivity test could take a minute or two depending on your connection strength.

On the Azure portal, navigate to the sync group created earlier and click "+ Add server endpoint"

![18](https://github.com/user-attachments/assets/c4c1a5e0-bfae-423c-abfd-7ed79eb36d91)

Select the on-prem server we just registered from the drop down, in my case "SRV-DC1.sixth-sensei.local". Next choose the path where the sync will be initiated from i.e all files in this path will be synced.

![19](https://github.com/user-attachments/assets/9a945b82-ef5b-48ef-ad74-3b5ccdf1e1c5)

You can also enable **cloud tiering**, this allows you to manage your local storage more efficiently by storing only frequently accessed files on your local server while infrequently accessed data still have a namespace on the server but content are retrieved from Azure File share when needed.

_Note:_

- Cloud tiering cannot be enabled on system volume partition (C:). 
- If the specified path does not exist, it is automatically created

Click on "create" to add the server endpoint

We can see our server is now added to the sync group and ready to sync

![20](https://github.com/user-attachments/assets/e86a983a-ace3-4517-96dd-5c920a33a219)

Also on the server itself, we can see the path created as it didn't exist

![server path empty](https://github.com/user-attachments/assets/9cbfcc91-b295-4d7b-a3d7-ee4ac005dfee)

Let's verify the setup by copying a file to the sync path

![server path populated](https://github.com/user-attachments/assets/d37e0f57-9811-45f1-99d5-f657c1104acc)

Go to the server-sync file share > Browse to verify the file exists

![21](https://github.com/user-attachments/assets/01357bce-d1be-44e4-89fd-dd53d1da7340)

Voila! 

Any file uploaded to the file share directly also gets replicated to the on-prem server completing a two-way sync and ensuring high availability.

