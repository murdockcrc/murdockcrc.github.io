---
layout: post
title: How to access local disk storage on Azure Functions for Python
date: '2019-10-20 07:52:22'
tags:
- azure
- cloud
- python
- functions
- azure-functions
---

If you try to access the local disk on Azure Functions for Python, you will be confronted with the following error:

    [Errno 30] Read-only file system: '/home/site/""wwwroot/""

As you might know from the Docs, Azure Functions for Python actually uses a container image to host your code. This Docker container is abstracted from you (you don't have to build it or configure it in anyway). However, as a managed serverless platform, Functions does impose certain limitations, and local disk storage is one of them.

In order to access the local disk, you need to fetch the _Temp_ directory on the host, like this:

    import tempfile
    
    import azure.functions as func
    
    def main(msg: func.ServiceBusMessage, outputblob: func.Out[str]):
        message = msg.get_body().decode('utf-8')
    
        dir_path = tempfile.gettempdir()
        // Save and read files on this tmp directory
        {your I/O code here}
        return

_tempfile.gettempdir()_ will get you a reference to the folder '/tmp', where you can write and read files from.

Keep in mind though, that Functions is conceived as a serverless platform, and you should not depend on local disk access to do any kind of persistence. Whatever you store in that location **will be deleted** on other Function invocations.

In our case, we are using _pyplot_ to create some charts based on IoT data, and we use the &nbsp;_dataframe.to\_csv()_ method to extract some of this data into a file for further analysis. These Python modules expect a path to local disk to save their output. However, our Function will later take these local files and output them to Blob storage.

Summary: if you need to access local disk on your Function app, you can do that using the _tmp_ directory. However, make sure you use local disk as temporary storage, and move those file to blob storage if you need to persist them after your Function invocation.

Happy coding!

