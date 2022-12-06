<!-- TABLE OF CONTENTS -->

<a href="https://www.nuget.org/packages/cloudlayerio-dotnet/"><img src="https://buildstats.info/nuget/cloudlayerio-dotnet"></a>
## Table of Contents

-   [About the Project](#about-the-project)
-   [Installation](#installation)
    -   [Visual Studio](#visual-studio)
    -   [JetBrains Rider](#jetbrains-rider)
    -   [Other](#other)
-   [Usage](#usage)

<!-- ABOUT THE PROJECT -->

## About The Project

`cloudlayerio-dotnet` is a **.NET 6** library [cloudlayer.io](https://cloudlayer.io). It is used to automate and manipulate content such as PDF files, and images.  Using this library and cloudlayer.io service, you can convert HTML to PDF, HTML to Images, URLs to PDFs, URLs to Images, and more.
## Installation

The `cloudlayerio-dotnet` library is bundled in a NuGet Package.

-   [NuGet Package](https://www.nuget.org/packages/cloudlayerio-dotnet/)

### Visual Studio

-   Using the [console](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-powershell)

    -   Run `Install-Package cloudlayerio-dotnet` in the console.

-   Using the [NuGet Package Manager](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-visual-studio)

    -   Search this package [NuGet Package](https://www.nuget.org/packages/cloudlayerio-dotnet) and install it.

### JetBrains Rider

-   <https://www.jetbrains.com/help/rider/Using_NuGet.html#>

### Other

-   <https://docs.microsoft.com/en-us/nuget/consume-packages/overview-and-workflow#ways-to-install-a-nuget-package>

<!-- USAGE EXAMPLES -->

## Setup
### Get your API Key
1.  To begin you will need an API Key. 
2. Create a free account at [cloudlayer.io](https://cloudlayer.io/auth/register) which will give you a chunk of free API credits to use for testing.

## Usage

### Initialize the Manager
```c#
var manager = new CloudlayerioManager("<YOUR-API-KEY>");
```

### Convert URL to PDF
```c#
var rsp = await manager.UrlToPdf(new UrlToPdf
{
    Url = "http://google.com"
});
```

The `rsp` is the [ReturnResponse](https://github.com/cloudlayerio/cloudlayerio-dotnet/blob/main/cloudlayerio-dotnet/responses/ReturnResponse.cs) type and will contain the `Stream` as well as several other properties, along with a helper method `SaveToFilesystem`. This method is a convenience method to make it easier to save the results of the stream to local storage.

As of v2, the PDF file is not returned as part of the response. The SDK now returns a JSON response and the SaveToFilesystem Helper will only save the json response to the filesystem now. In addition, the response now contains the entire response object which is populated with the response data in a fully typed manner.

If you are using `async: false` it will return back the entire populated response, including the `assetUrl` which will contain the URL to your asset.

### Save Response to Local Storage
```c#
await rsp.SaveToFileSystem("C:\myfile.json");
```

### Get the Url
_Note: This will be empty for async calls, use webhook to get the response for asnyc. Otherwise, use `async: false` property._
```c#
var url = rsp.Response.AssetUrl;
```

### Set Async to false
If you do not plan to use webhooks, and have short lived requests you can set `async: false`. If your requests are long lived, we highly suggest using webhooks with `async: true` to avoid connection terminations due to timeouts.
```c#
var rsp = await manager.UrlToImage(new UrlToImage {
    Url = "http://google.com",
    Async = false
});
var url = rsp.Response.AssetUrl;
```

You of course can access the `Stream` property your self and write your own storage code.

### Passing in more options
In the previous example, we didn't pass in any optional parameters and left all the defaults. There are a significant amount of options to choose from.  Take a look them all by looking at the [requests folder](https://github.com/cloudlayerio/cloudlayerio-dotnet/tree/main/cloudlayerio-dotnet/requests) which will give you a good idea of the available options for each endpoint.

### Url to Image With Options

```c#
var rsp = await manager.UrlToImage(new UrlToImage {
    Url = "http://google.com",
    AutoScroll = true,
    ViewPort = new ViewPort {
        Height = 2560,
        Width = 1440,
        DeviceScaleFactor = 2
    }
});

var url = rsp.Response.AssetUrl;
```

### Advanced Options
We have made it easy to pass in the more advanced options by creating types that will serialize correctly.  This makes it significantly easier for you to just write the code and not worry about anything else.  

```c#
var rsp = new manager.UrlToPdf(new UrlToPdf {
    Url = "https://google.com",
    Margin = new Margin
    {
        Bottom = new LayoutDimension(UnitTypes.Pixels, 100),
        Top = new LayoutDimension(UnitTypes.Pixels, 100),
        Left = new LayoutDimension(UnitTypes.Pixels, 100),
        Right = new LayoutDimension(UnitTypes.Pixels, 100)
    },
    FooterTemplate = new HeaderFooterTemplate {
        Selector = "#myDiv",
        Style = new Dictionary<string, string>
        {
            ["padding-bottom"] = "10px",
            ["height"] = "40px"
        },
        ImageStyle = new Dictionary<string, string>
        {
            ["padding"] = "20px",
            ["border"] = "thick double red"
        }
    }
});
```
That's just an example of adding a bunch of options.  For more information on what each option does you can take a look at our [docs](https://cloudlayer.io/docs) or look at the source code as it's heavily commented on what each property does and how to use it.  It will also show up as intellisense info as you use the properties.

## More information

Check the [Tutorials](https://docs.cloudlayer.io) page to get started.

