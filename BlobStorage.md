# Blob Storage

Azure Blob storage is optimized for storing massive amounts of unstructured data.  Unstructured data is data that does not adhere to a particular data model or definition, such as text or binary data.  Blob storage offers three types of resources:

-   The storage account
-   A container in the storage account
-   A blob in the container


[Quickstart guide](https://docs.microsoft.com/de-de/azure/storage/blobs/storage-quickstart-blobs-dotnet)

The **.NET Client** works with the following 4 classes:

- BlobServiceClient](https://docs.microsoft.com/de-de/dotnet/api/azure.storage.blobs.blobserviceclient): The  `BlobServiceClient`  class allows you to manipulate Azure Storage resources and blob containers.
  [
- BlobContainerClient](https://docs.microsoft.com/de-de/dotnet/api/azure.storage.blobs.blobcontainerclient): The  `BlobContainerClient`  class allows you to manipulate Azure Storage containers and their blobs.
  
- [BlobClient](https://docs.microsoft.com/de-de/dotnet/api/azure.storage.blobs.blobclient): The  `BlobClient`  class allows you to manipulate Azure Storage blobs.
 
- [BlobDownloadInfo](https://docs.microsoft.com/de-de/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): The  `BlobDownloadInfo`  class represents the properties and content returned from downloading a blob.

*This is for the v12 of the .NET Client*
`Azure.Storage.Blobs`
*The older v11 has a different nuget package*
`WindowsAzure.Storage.Blobs`


## Tricks
**Storage emulator**


There is a [storage emulator](https://docs.microsoft.com/de-de/azure/storage/common/storage-use-emulator) which comes with the [Azure CLI tools](https://docs.microsoft.com/de-de/cli/azure/install-azure-cli-windows?view=azure-cli-latest).
This can be started by searching for it in the windows start menu.

Instead of specifying an entire connection string, there is a short cut to use the storage emulator `connectionString = "UseDevelopmentStorage=true"`.



## Snippets
**Create a container**
``` csharp 
var client = new BlobContainerClient(CONNECTION_STRING, CONTAINER_NAME);

//MAKE SURE to create the new container with public access
//Otherwise it wont be accessible by URL
client.CreateIfNotExists(PublicAccessType.Blob);
_containerClient = client;
``` 


**Upload to a blob**
```csharp
//Use an existing container client if possible
var client = _containerClient.GetBlobClient(BLOB_NAME);

//It is also possible to upload via filepath directly
using (FileStream stream = File.OpenRead(FILEPATH))
{
	//Creates blob, or overwrites existing blob
	await client.UploadAsync(stream);
}
```

**Download from a blob**

```csharp
//Create a new blob client by URI
//This only works if the blob is publicly accessible,
// otherwise a 404 is returned
var blobClient = new BlobClient(new Uri(blobURI));
//or get one from an existing container client
var blobClient = _containerClient.GetBlobClient(BLOB_NAME);


//Set up a stream to download to
using (FileStream stream = File.OpenWrite(FILEPATH))
{
	//Write a blob to stream
	await client.DownloadToAsync(stream);
}
```


Further code snippets can be found on their [**Github**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs).


## Authentication

If a container is not created with public access then there are multiple different ways to authenticate for access.

One example is a [shared access signature key](https://docs.microsoft.com/de-de/azure/storage/blobs/storage-blob-user-delegation-sas-create-dotnet). 

