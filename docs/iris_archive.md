# Iris Archive

## Overview
All files sent out across IRIS will additionally be stored within the IRIS Archive. The archive requires no authentication allowing quick data retrieval.

Our CDN implements caching of files accessed within the past year and file compression to again speed up the service.

## Azure Storage Explorer

To easily browse the IRIS Archive with a graphic interface, Azure Storage Explorer can be used.

### Setup

1. Download and install Azure Storage Explorer from [here](https://azure.microsoft.com/en-gb/features/storage-explorer/).
2. Open Azure Storage Explorer and click on the `Attatch to a resource` button.
3. Select `Blob container or directory`.
3. Select `Anonymous access`.
4. Enter the following blob container URL: `https://archive.data.elexon.co.uk/iris-archive`.
5. Confirm connecting to resource.

### Downloading files via Azure Storage Explorer
- azCopy V10.22.0 is needed, by default ASE uses V10.20. To fix this download azCopy V10.22.0 [here](https://aka.ms/downloadazcopy-v10-windows).
- Locate the bin folder within where you installed Azure Storage Explorer at `Microsoft Azure Storage Explorer\resources\app\node_modules\@azure-tools\azcopy-win64\dist\bin`.
- Swap the existing azCopy file with the new one, changing the new file's name to match the existing one.

### Cached files

For each dataset Azure Storage Explorer caches 5000 files, and only shows these by default. To load additional files the `Load more` button must be pressed.

## Azure SDKs

The IRIS Archive can also be interacted with programmatically via Azure SDK's, see more information [here](https://azure.microsoft.com/en-gb/downloads/).

### CDN range header workaround

Microsoft CDN only accepts the standard HTTP range header whereas the SDK uses the `x-ms-range` range header in all range requests.

To fix this we can manually convert the header in our code to the desired type (see `range_request_hook` in the example) and pass this converted header to our `BlobServiceClient`.

### Example code

See below code to retrieve a file using the Python SDK.

```
import asyncio

from azure.storage.blob.aio import StorageStreamDownloader, BlobServiceClient

def range_request_hook(request):
    range_value = request.http_request.headers.pop('x-ms-range', None)
    if range_value:
        request.http_request.headers['Range'] = range_value


async def download_blob():
    iris_archive_url = "https://archive.data.elexon.co.uk/"
    blob_path = f"data/ABUC/ABUC_202311140741_NGET-EMFIP-ABUC-00689167-1.json"
    async with BlobServiceClient(iris_archive_url, raw_request_hook=range_request_hook) as blob_service_client:
        async with blob_service_client.get_blob_client("iris-archive", blob_path) as blob_client:
            blob_downloader: StorageStreamDownloader = await blob_client.download_blob(
                encoding="UTF-8"
            )
            contents = await blob_downloader.readall()
            print(contents)


asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())
asyncio.run(download_blob())

```


