# azure-page-stream
Expriemental azure page stream aimed at loggers (and "live" logs)

## Option 1 Page Blobs

  1. You need to give it a size prior to writing (you don't pay for empty "pages")
  2. Must write in byte sizes divisible by 512

### Strategy 

(The client may work great for this with some more tinkering but it did not work very well out of the box)

  - Buffer streams up to 4mb in size in a time bounded fashon (configurable)
  - Ensure when stream is "ended" the last buffer is some number of bytes divisible by 512
  - Resize? The blob to a reasonable number

## Option 2 Block Blobs

Block blobs don't have any weird byte chunk limitations but need to be staged into a "pending" state prior to being commited. The only concern here is the overhead required to manage the committing of chunks in the blob (many more api calls).

During my testing block blobs actually worked fairly well but had some extra bookkeeping requirements.

  - Each put must have a block id (and all block ids must be lexically ordered but of the same length)
  - Each Buffer must be staged (with the above blob id) and then committed later in a seperate operations
  - All block ids must be kept track of for all time because the "commit" operation requires a complete list of   blockIds to construct the final blob.

## Links

 - [Azure Blob Api] (http://msdn.microsoft.com/en-us/library/windowsazure/dd135733.aspx)
 - [Node lib] (https://github.com/WindowsAzure/azure-sdk-for-node)
