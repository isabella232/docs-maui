---
title: "BlazorWebView"
description: "The .NET Multi-platform App UI (.NET MAUI) BlazorWebView control enables you to host a Blazor web app in your .NET MAUI app, and integrate the app with device features."
ms.date: 08/19/2021
---

# BlazorWebView

The .NET Multi-platform App UI (.NET MAUI) `BlazorWebView` is a control that enables you to host a Blazor web app in your .NET MAUI app. These apps, known as .NET MAUI Blazor apps, enable a Blazor web app to be integrated with native platform features and UI controls. The `BlazorWebView` control can be added to any page of a .NET MAUI app, and pointed to the root of the Blazor app. The Blazor components run natively in the .NET process and render web UI to an embedded web view control. .NET MAUI Blazor apps can run on all the platforms supported by .NET MAUI.

> [!IMPORTANT]
> Using the `BlazorWebView` control requires you to have installed the ASP.NET and web development workload in Visual Studio. In addition, to use the `BlazorWebView` control on Windows requires you to install the [WebView2](https://developer.microsoft.com/microsoft-edge/webview2/) runtime.

`BlazorWebView` defines the following properties:

- `HostPage`, of type `string?`, which defines the root page of the Blazor web app.
- `RootComponents`, of type `ObservableCollection<RootComponent>`, which specifies the collection of root components that can be added to the control.

The `RootComponent` class defines the following properties:

- `Selector`, of type `string?`, which defines the CSS selector string that specifies where in the document the component should be placed.
- `ComponentType`, of type `Type?`, which defines the type of the root component.
- `Parameters`, of type `IDictionary<string, object?>?`, which represents an optional dictionary of parameters to pass to the root component.

For information about Blazor apps, see [Introduction to ASP.NET Core Blazor](/aspnet/core/blazor/).

> [!NOTE]
> Existing Blazor components can be used in a .NET MAUI Blazor app by moving the code into the app, or by referencing an existing class library or package that contains the component.

## Create a .NET MAUI Blazor app

A .NET MAUI Blazor app can be created in Visual Studio by the **.NET MAUI Blazor app** template:

:::image type="content" source="blazorwebview-images/blazorwebapp-template.png" alt-text=".NET MAUI Blazor app project template screenshot.":::

This project template creates a multi-targeted .NET MAUI Blazor app that can be deployed to Android, iOS, Mac Catalyst, and Windows. The `BlazorWebView` is defined in *MainPage.xaml* in the created project, and points to the root of the Blazor app:

```xaml
<ContentPage xmlns=http://schemas.microsoft.com/dotnet/2021/maui
             xmlns:x=http://schemas.microsoft.com/winfx/2009/xaml
             xmlns:b="clr-namespace:Microsoft.AspNetCore.Components.WebView.Maui;assembly=Microsoft.AspNetCore.Components.WebView.Maui"
             xmlns:local="clr-namespace:BlazorWebViewDemo"
             x:Class="BlazorWebViewDemo.MainPage"
             BackgroundColor="{DynamicResource PageBackgroundColor}">

    <b:BlazorWebView HostPage="wwwroot/index.html">
        <b:BlazorWebView.RootComponents>
            <b:RootComponent Selector="app" ComponentType="{x:Type local:Main}" />
        </b:BlazorWebView.RootComponents>
    </b:BlazorWebView>

</ContentPage>
```

The root Blazor component for the app is in *Main.razor*. The rest of the Blazor components are in the *Pages* and *Shared* project folders, and are identical to the components used in the default Blazor template. Static web assets for the app are in the *wwwroot* folder.

## Add a BlazorWebView to an existing app

The process to add a `BlazorWebView` to an existing .NET MAUI app is as follows:

1. Add the Razor SDK, `Microsoft.NET.Sdk.Razor` to your project by editing its first line:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Razor">
    ```

    The Razor SDK is required to build and package projects containing Razor files for Blazor projects.

1. Add the root Blazor component for the app to the project.
1. Add your Blazor components to project folders named *Pages* and *Shared*.
1. Add your static web assets to a project folder named *wwwroot*.
1. Add any optional *_Imports.razor* files to your project.
1. Add a `BlazorWebView` to a page in your .NET MAUI app, and point it to the root of the Blazor app:

    ```xaml
    <ContentPage xmlns=http://schemas.microsoft.com/dotnet/2021/maui
                 xmlns:x=http://schemas.microsoft.com/winfx/2009/xaml
                 xmlns:b="clr-namespace:Microsoft.AspNetCore.Components.WebView.Maui;assembly=Microsoft.AspNetCore.Components.WebView.Maui"
                 xmlns:local="clr-namespace:MyBlazorApp"
                 x:Class="MyBlazorApp.MainPage">

        <b:BlazorWebView HostPage="wwwroot/index.html">
            <b:BlazorWebView.RootComponents>
                <b:RootComponent Selector="app" ComponentType="{x:Type local:Main}" />
            </b:BlazorWebView.RootComponents>
        </b:BlazorWebView>

    </ContentPage>
    ```

1. Modify your `Startup` class to add `using` directives for the `Microsoft.AspNetCore.Components.WebView.Maui` and `Microsoft.Extensions.DependencyInjection` namespaces:

    ```csharp
    using Microsoft.AspNetCore.Components.WebView.Maui;
    using Microsoft.Extensions.DependencyInjection;
    ```

    > [!NOTE]
    > Blazor requires service scopes, which are only supported with the `Microsoft.Extensions.DependencyInjection` library.

1. Modify the `Configure` method of your `Startup` class to register the `BlazorWebView` control for use in your app. To do this, call the `RegisterBlazorMauiWebView` method, the `UseMicrosoftExtensionsServiceProviderFactory` method, and the `ConfigureServices` method on the `IAppHostBuilder` object. Then, on the `IServiceCollection` object, call the `AddBlazorWebView` method to add component web view services to the services collection:

    ```csharp
    public class Startup : IStartup
    {
           public void Configure(IAppHostBuilder appBuilder)
           {
                 appBuilder
                        .RegisterBlazorMauiWebView()
                        .UseMicrosoftExtensionsServiceProviderFactory()
                        .UseMauiApp<App>()
                        .ConfigureFonts(fonts =>
                        {
                            fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
                        })
                        .ConfigureServices(services =>
                        {
                            services.AddBlazorWebView();
                            // Add additional services
                        });
           }
    }
    ```