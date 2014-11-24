---
layout: page
title : What's New in 1.7.5
group: Changelogs
weight: 2

---

We are happy to announce the release of **Awesomium.NET 1.7.5b**. This is a **beta** release with many new **_experimental_** features and enhancements.

* [Download the Awesomium 1.7.5b SDK](http://www.awesomium.com/download) (includes the **Awesomium.NET** binaries and samples)
* [Awesomium.NET 1.7.5 API Reference](http://docs.awesomium.net)
* [Setting up on Windows](http://wiki.awesomium.net/getting-started/setting-up-on-windows.html)
* [Setting up on Mac OS X](http://wiki.awesomium.net/getting-started/setting-up-on-mac-osx.html)


## Major New Features

With this release we are introducing many important new features and improvements. Some of them may require refactoring your code.

> <span style="color:red;">Please read: **[Breaking API Changes in v1.7.5](breaking-changes.html)** for a list and presentation of important API changes in version 1.7.5 that require refectoring your code.</span>

Here is a short presentation of some of these new features and improvements:

### Optimizations

All throughout the **Awesomium.NET** project, a series of **code optimizations have been applied that significantly improve the overall performance of Awesomium.NET components**.

* Pages and resources are now loaded faster.
* Overall memory usage improved.
* Rendering and resizing in *offscreen* surfaces is performed faster, using less resources.
* Interaction with pages using JavaScript is significantly improved (also see *New Javascript Integration Features* below).
* Awesomium.NET now logs messages to the log file (see: [`LogPath`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_LogPath) and [`LogLevel`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_LogLevel)). Exceptions and their stack trace are also logged. 
* Performance of Awesomium.NET's *synchronization context* (for non-UI environments and cross-thread interaction) has been improved and more features are added.

### New Javascript Integration Features

**Awesomium.NET v1.7.5** adds new features and improvements to Awesomium's Javascript Integration API. New features improve performance, allow easier interaction with the page and of the page with the hosting application and make the code needed to interact using JavaScript shorter, simpler and safer.

This is only a short list of the new features:

* JavaScript-related event handlers and custom JavaScript method handlers, are now called in a **Javascript Execution Context (JEC). Any `JSObject` instances passed, acquired or created in a JEC, are automatically disposed upon exiting the method (handler)** associated with the execution context. You no longer need to explicitly dispose JSObjects in those methods (by wrapping code with `using` statements for example).
* **Handlers executed in an asynchronous JEC** (handlers of asynchronous JavaScript-related event or asynchronous custom JavaScript methods), **have immediate access to essential JavaScript objects of the loaded page's current JavaScript environment (such as *`window`*, *`document`* or the generics of *`Object`*)**, through [`Global`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Global). Applications no longer need to perform additional synchronous calls to acquire these objects (using [`ExecuteJavascriptWithResult`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_IWebView_ExecuteJavascriptWithResult) for example).
* Binding errors, JavaScript errors or **exceptions that occur in code executed in a JEC, are silently handled and propagated to the JavaScript console** (see: [`ConsoleMessage`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ConsoleMessage)).
* Users can now derive `JSObject` (it is no longer *`sealed`*), to create custom local JSObjects to pass to the page. The new Javascript Integration API allows **Awesomium.NET** cast these objects back to their original subclass, when they are re-acquired from V8.
* JavaScript clients can now interact with the hosting application through the API of the new **Javascript Interoperation Framework (JIF). The framework provides methods to acquire information about the hosting application and the running views, monitor global (`WebCore`) or `IWebView` events, control views (even views different than the one hosting the page), control the hosting UI (if any) and send messages to the hosting application synchronously or asynchronously, passing data and JavaScript objects, even when sending messages synchronously**. The hosting application can control the features available to JavaScript code through new settings added to [`WebPreferences`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebPreferences). JIF provides most of the API a web-page would need to communicate with the application. This way applications don't have to design this interface themselves and they avoid numerous synchronous calls to the child-process to create global JavaScript objects (using [`CreateGlobalJavascriptObject`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_CreateGlobalJavascriptObject)) and bind to custom JavaScript methods.
* **Dynamic Language Runtime (DLR)** support on `JSObject` has been significantly improved. **Users can now pass managed handlers directly as arguments to a dynamic JavaScript expression, to be used as callbacks**. In C#, the JavaScript objects provided to an asynchronous handler through `Global`, are already of type *`dynamic`*. The overall performance and reliability of DLR on `JSObject` has also been improved and basic DLR support has been added to `JSValue`.
* All dynamic expressions now return either `JSValue` or `JSObject` (and subclasses of it). Unary and binary operations support has been added to `JSValue` and many new casting operators and features are added to both `JSValue` and `JSObject` that simplify code.
* [`JSFunction`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction), a custom subclass of `JSObject` has been added to the API. **JavaScript objects of type *`function`* acquired from the page, are wrapped as `JSFunction` and implicit casting from `JSValue` is available**.
* **`JSObject` is now enumerable**. You can iterate through the ECMAScript enumerable property names of a JavaScript object as you would in JavaScript.
* **JSObject's indexer now also returns members of type *`function`* (methods) of a remote JavaScript object**.
* `JSObject` now allows users to **specify or acquire the ECMAScript property descriptor of a JavaScript object's property** (see: [`JSPropertyDescriptor`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSPropertyDescriptor), [`JSObject[String,...,JSPropertyDescriptor]`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSObject_Property_3) and [`JSObject.GetPropertyDescriptor`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_GetPropertyDescriptor)).
* You can now **check for `NaN` and `Infitity` JavaScript values through `JSValue`**.
* An indexer has been added to `JSValue` that among other features, allows users **edit JavaScript arrays directly from `JSValue`**.
* **Awesomium.NET's new Javascript Integration fixes issues with Awesomium's DOM**. For example, **JavaScript *`navigator.language`* now correctly reports a value related to the hosting application's `CultureInfo`** and extension methods have been added to the `Utilities` of technology-specific assemblies that help you set the application's culture and thus also control the appearance of web applications that rely on *`navigator.language`*. Also, **the missing *`click`* method has been added to the prototype of `HTMLAnchorElement`** that allows JavaScript simulate a click to a link. 
* **Touch events are now properly fired on JavaScript** when using the WPF `WebControl` with a multi-touch surface (or code the new [`InjectTouchEvent`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_InjectTouchEvent) method with any web-view component). Event listener assignment properties for **touch events** have been added to `Node`, the base class of all DOM elements.
* Many more fixes and reliability improvements under the hood.

**For more details and presentation of the revemped Javascript Integration API, read the following articles**:

* [Introduction to JavaScript Integration](../javascript/introduction.html)
* [Synchronous & Asynchronous API](../javascript/sync_async_js_api.html)
* [Javascript Execution Context (JEC)](../javascript/jec.html)
* [Dynamic Language Runtime (DLR) Support](../javascript/dlr.html)
* [Javascript Interoperation Framework (JIF)](http://docs.awesomium.net/?tc=R_Project_OSMJIFHelp)

### New WPF Design-Time Support

Three new WPF Designer support assemblies have been added to the SDK that provide design-time features for **Awesomium.NET** WPF components for **Visual Studio 2010, 2012 and 2013**.

Here's a list of the new design-time support features provided to WPF components:

* You can now **edit the [`WebPreferences`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebSessionProvider_Preferences) of a [`WebSessionProvider`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebSessionProvider) directly from the Properties window** when a `WebSessionProvider` is selected in XAML (even if a `WebPreferences` element is not added in XAML). Even if properties in the Properties window are not sorted by Category, **a dialog is available for editing `WebPreferences`**.
* **Default values of preferences are shown to the Properties window** or dialog and **tooltips with descriptions for each setting are available**.
* A dialog has been added that allows you to **see and edit the properties of `DataSources` of a [`WebSessionProvider`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebSessionProvider) when it's selected in XAML**.
* **New Properties window editors** have been added for most of the properties of a WPF [`WebControl`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebControl).
* Awesomium.NET's designer of a `WebControl` now controls the settings and the availability of properties of a `WebControl` in the Properties window, based on other settings of the application or the `WebControl` itself.
* WebControl's editor of the [`WebSession`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebControl_WebSession) property in the Properties window provides a drop-down menu that allows you to select from existing `WebSessionProvider` resources. Upon selection, **XAML is automatically generated**.
* When you drag and drop a `WebControl` from the Visual Studio toolbox in Visual Studio 2012 and 2013, the control automatically performs initialization and **fills the parent container in the designer**. 

*Install the SDK now and explore the new WPF design-time features*.

As always, don't forget to **download the ClickOnce WPF demo, from [here](http://awesomium.amadeusoft.com/wpf/setup.exe)**.

### PDF Files Viewer

**Awesomium.NET v1.7.5** incorporates **[PDF.js](http://mozilla.github.io/pdf.js/)**. When you navigate to a PDF file, Awesomium.NET's **PDF.js** implementation is loaded and shows the PDF file in any Awesomium.NET web-view component.

> This technology is **experimental**.

* Users can still **download the original PDF file** using the **Download** button available in the viewer's toolbar.
* **Awesomium.NET** automatically replaces HTML `<object>` tags in a page that attempt to load Adobe's PDF Reader plugin (that is officially not supported by Awesomium), with an `<iframe>` that loads Awesomium.NET's **PDF.js** implementation.
* Users can **enable or disable PDF.js support** through [`WebPreferences.PdfJS`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_PdfJS).
* The **PDF viewer is automatically localized** based on the value of *`navigator.language`*, thus based on the application's current `CultureInfo` (see new Javascript Integration features above).

*Run an Awesomium.NET sample or demo now, and [navigate to the PDF version of this CHANGELOG](whats-new-1-7-5.pdf)*.

### Asynchronous ResourceInterceptor

A single powerful new API member, [`IgnoreDataSources`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ResourceRequest_IgnoreDataSources), now allows users to **asynchronously load resources for remote pages**, combining the power of an [`IResourceInterceptor`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceInterceptor) implementation and of a custom [`DataSource`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Data_DataSource).

Members of an [`IResourceInterceptor`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceInterceptor) are called in the I/O thread and providing a response must be performed synchronously (actually, delaying the I/O thread in any way can cause all sorts of side effects). However, custom DataSources (designed to load local pages and resources on a web-view), can provide responses and resources asynchronously. Using the available new API, users can now combine the power of these two classes to asynchronously load resources for remote pages:

#### Simple WebView Example:

1. Create a custom [`DataSource`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Data_DataSource) (or [`AsyncDataSource`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Data_AsyncDataSource) or use any of the predefined [**Awesomium.NET** DataSources](http://docs.awesomium.net/?tc=N_Awesomium_Core_Data) to load resources from assembly resources, a local directory or a compressed PAK file). 
2. Implement [`OnRequest`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Data_DataSource_OnRequest) (or [`LoadResourceAsync`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Data_AsyncDataSource_LoadResourceAsync) respectively) to asynchronously provide one or more resources upon certain requests. (Skip this step if you use a [predefined `DataSource`](http://docs.awesomium.net/?tc=N_Awesomium_Core_Data).)
3. Explicitly [initialize the `WebCore`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Initialize) specifying *`"http"`* or *`"https"`* as [`AssetProtocol`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_AssetProtocol).
4. [Create a `WebSession`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_WebCore_CreateWebSession) and [add your custom `DataSource`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebSession_AddDataSource) using [`CATCH_ALL`](http://docs.awesomium.net/?tc=F_Awesomium_Core_Data_DataSource_CATCH_ALL) as host name.
5. [Create a new `WebView`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_CreateWebView_1) using the created `WebSession`.
  
    Now, all requests for and from remote pages (using the *`"http"`* or *`"https"`* protocol), irrespective of hostname (domain name), will be directed to your custom *catch-all* `DataSource`. But you cannot (and you don't want to) provide all resources from local assets.
6. Implement [`IResourceInterceptor`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceInterceptor) and assign your implementation to [`WebCore.ResourceInterceptor`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_ResourceInterceptor). Even when you use DataSources, all requests targeting a `DataSource` are still passing from `IResourceInterceptor` before reaching [`DataSource.OnRequest`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Data_DataSource_OnRequest).
7. In your implementation of [`IResourceInterceptor.OnRequest`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IResourceInterceptor_OnRequest), check the value of [`ResourceRequest.Url`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ResourceRequest_Url) (or any other parameters you want to evaluate). If the request targets a resource that you want to load asynchronously, set [`IgnoreDataSources`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ResourceRequest_IgnoreDataSources) to *`false`*; for all other requests, set `IgnoreDataSources` to *`true`*. `IgnoreDataSources` tells Awesomium to ignore any DataSources registered for this asset protocol and hostname, and process the request normally (which means the request will be sent to the remote server).

This way you can load only certain resources asynchronously while let the rest of the resources be normally loaded from the remote server.

**For a sample of this scenario, see the Windows Forms _WinFormsSample_ available with the SDK**. The sample code (see: *`WebForm.cs`*) can be used with any technology and web-view component (WPF, MonoMac etc.).


## New Features

#### Core

* Code optimizations and performance improvements all throughout the **Awesomium.NET** project.
* Added logging of **Awesomium.NET** events to application log.
* Improved initial position *specs* for JavaScript `window.open` calls.
* Improved JIF to assist native `DocumentReady`, improve `HTML` property contents and handle pending `window.close` calls.
* Added **PDF.js** integration.
* Added support for environment variables in `DataPath`, `LogPath` etc.
* Improved performance of `WebCore.QueueWork`.
* Added support for JavaScript `navigator.language`.
* Added support for DOM `HTMLAnchorElement.click`.
* Added standard *`onXXXX`* touch event handler setting properties to JavaScript `Node` prototype.
* Improved `JSValue` **->** `bool` operator to process all types.
* Added binary and unary operators to `JSValue`.
* `JSValue` is now a class.
* Many performance improvements in `ResourceInterceptor`.
* Added support for asynchronous `ResourceInterceptor` responses through `ResourceRequest.IgnoreDataSources` and DataSources.
* Completely redesigned (re-wrote) JIF using supported ECMAScript 5 features.
* Designed and created the `OSMJIF` instance that exposes a fully operational Javascript Framework to JavaScript clients.
* `OSMJIF` API allows clients obtain global and per-view information.
* `OSMJIF` API allows clients add listeners for global or per-view native events.
* `OSMJIF` API allows clients control parts of the native application.
* `OSMJIF` extends the DOM to handle all multi-touch-related features (such as scrolling).
* `OSMInfo`, `OSMEventArgs` and `OSMView` fully configured JavaScript prototypes part of the new API.
* Made it so Javascript-related events and custom JavaScript method handlers are called in a **Javascript Execution Context (JEC)**.
* `JSObject` instances acquired or created in a **Javascript Execution Context (JEC)** don't need to explicitly disposed.
* Errors or exceptions that occur in a **Javascript Execution Context (JEC)** are silently propagated to the JavaScript console.
* Made it so most `JSObject` operations are first handled by JIF, if available, significantly improving performance.
* Made it so implicit casting of `JSValue` to `JSObject` or `JSFunction` always succeeds returning an invalid object.
* Added full support for dynamically indexing JSObjects.
* Made it so local JSObjects hold members in internal managed dictionaries.
* Made it so all dynamic expressions on `JSObject` return either `JSValue` or `JSObject`.
* Many optimizations and performance improvements on `JSObject`.
* `JSObject` is not sealed any more.
* Added `JSObject` indexer overloads that take a `JSPropertyDescriptor`.
* `JSObject` is now enumerable (enumerates ECMAScript enumerable property names).
* Made it so users can restore local JSObjects back to their original subclass when reacquired from V8.
* Most late binding errors on `JSObject` (in DLR) no longer throw an exception.
* Added dynamic conversion support to `JSObject`.
* Added support for passing managed handlers as callbacks directly to dynamic expressions.
* Added basic support of DLR to `JSValue`.
* Added indexer to `JSValue` that allows accessing and editing arrays, objects and strings.
* Added support for passing objects through the synchronous `OSMJIF.sendMessage`.
* Made it so all methods or events executed in a JEC have access to essential JavaScript objects (through `Global`).
* More **VB.NET** DLR improvements.
* A single background thread is now handling auto-update in `InAutoUpdate` mode.
* Significantly improved and added more documentation.

#### WPF

* Added *`http://schemas.awesomium.com/core`* **XMLNS** schema for Core assembly.
* Added WPF Designer extensions for `WebControl` and `WebSessionProvider` for Visual Studio 2010, 2012 and 2013.
* Added full WPF Touch/Stylus support.
* Made it so we cancel touch manipulation when Javascript is disabled.
* Made it so mouse is only injected on tap or after press-and-hold.
* Added support for scrolling (touch-drag) parent scrollable elements instead of the view.

#### Unity

* Added `WebSessionProvider` component.
* Implemented Undo/Redo for Inspector changes.


## API Changes

### New API:

#### Awesomium.Core

* [`DocumentReadyState`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyState)
* [`DocumentReadyEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventArgs)
* [`DocumentReadyEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventHandler)
* [`WebTouchEvent`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebTouchEvent)
* [`WebTouchEventType`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebTouchEventType)
* [`WebTouchPoint`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebTouchPoint)
* [`WebTouchPointState`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebTouchPointState)
* [`NativeHandle`](http://docs.awesomium.net/?tc=T_Awesomium_Core_NativeHandle)
* [`WebCore.DoWork`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_WebCore_DoWork)
* [`WebCore.UsedMemory`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_UsedMemory)
* [`WebCore.StartTime`]()
* [`WebCore.ReleaseMemory`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_ReleaseMemory)
* [`WebCore.AllocatedMemory`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_AllocatedMemory)
* [`WebCore.UsedMemory`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_UsedMemory)
* [`WebCore.Run(CoreStartEventHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Run_1)
* [`WebConfig.ASSET_PROTOCOL_DEFAULT`](http://docs.awesomium.net/?tc=F_Awesomium_Core_WebConfig_ASSET_PROTOCOL_DEFAULT)
* [`WebConfig.CustomCSS`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_CustomCSS)
* [`WebPreferences.MaxHttpCacheStorage`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_MaxHttpCacheStorage)
* [`WebPreferences.PdfJS`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_PdfJS)
* [`WebPreferences.UserScript`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_UserScript)
* [`WebPreferences.JavascriptViews`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptViews)
* [`WebPreferences.JavascriptApplicationInfo`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptApplicationInfo)
* [`WebPreferences.JavascriptGlobalEvents`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptGlobalEvents)
* [`WebPreferences.JavascriptViewEvents`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptViewEvents)
* [`WebPreferences.JavascriptViewExecute`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptViewExecute)
* [`WebPreferences.JavascriptViewChangeSource`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPreferences_JavascriptViewChangeSource)
* [`JavascriptRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptRequest)
* [`JavascriptMessageEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMessageEventArgs)
* [`JavascriptRequestEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptRequestEventArgs)
* [`JavascriptMessageEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMessageEventHandler)
* [`JavascriptRequestEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptRequestEventHandler)
* [`JavascriptExecutionContextMethod`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptExecutionContextMethod)
* [`JavascriptExecutionContextMethod<T>`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptExecutionContextMethod_1)
* [`IWebView.JavascriptRequest`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_JavascriptRequest)
* [`IWebView.JavascriptMessage`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_JavascriptMessage)
* [`IWebView.CreateJavascriptExecutionContext`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_IWebView_CreateJavascriptExecutionContext)
* [`IWebView.InjectTouchEvent`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_InjectTouchEvent)
* [`IWebView.CreationTime`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_CreationTime)
* [`JSFunction`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction)
* [`JSObject.GetPropertyDescriptor`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_GetPropertyDescriptor)
* [`JSObject[..., JSPropertyDescriptor]`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_JSObject_Property)
* [`JSObject.BindAsync`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_JSObject_BindAsync)
* [`JSValue[...]`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_Item)
* [`JSValue.IsNaN`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsNaN)
* [`JSValue.IsInfinity`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsInfinity)
* [`JSValue.IsFunctionObject`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsFunctionObject)
* [`JSValue (All Unary & Binary operators)`](http://docs.awesomium.net/?tc=Operators_T_Awesomium_Core_JSValue)
* [`Global`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Global)
* [`DocumentReadyEventArgs.Environment`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_Environment)
* [`JavascriptMethodHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMethodHandler)
* [`JavascriptAsyncMethodHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptAsyncMethodHandler)
* [`JSFunctionHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunctionHandler)
* [`JSFunctionAsyncHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunctionAsyncHandler)
* [`JSPropertyDescriptor`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSPropertyDescriptor)
* [`ResourceRequest.IgnoreDataSources`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ResourceRequest_IgnoreDataSources)
* [`WebContextMenuInfo.IsEmpty`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebContextMenuInfo_IsEmpty)

#### Awesomium.Windows.Controls (WPF)

* [`WebControlService.PressAndHoldDelay`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebControlService_PressAndHoldDelay)
* [`Utilities.SetCulture`](http://docs.awesomium.net/?tc=M_Awesomium_Windows_Controls_Utilities_SetCulture)

#### Awesomium.Windows.Forms (Windows Forms)

* [`Utilities.SetCulture`](http://docs.awesomium.net/?tc=M_Awesomium_Windows_Forms_Utilities_SetCulture)

#### JavaScript

* [`OSMJIF`](http://docs.awesomium.net/?tc=T_global_OSMJIF)
* [`OSMInfo`](http://docs.awesomium.net/?tc=T_global_OSMInfo)
* [`OSMEventArgs`](http://docs.awesomium.net/?tc=T_global_OSMEventArgs)
* [`OSMView`](http://docs.awesomium.net/?tc=T_global_OSMView)

### Modified API:

#### Awesomium.Core

* [`IWebView.DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady)
* [`IWebView.Instance`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_Instance) -> [`NativeHandle`](http://docs.awesomium.net/?tc=T_Awesomium_Core_NativeHandle)
* [`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue) **_struct_** -> **_class_**

### Obsolete API:

#### Awesomium.Core

* [`JSObject.Bind(String,Boolean,JavascriptMethodEventHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_4)
* [`JavascriptMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMethodEventHandler)
* [`JavascriptAsynchMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptAsynchMethodEventHandler)


## Bug Fixes

#### Native Awesomium

* Fixed crash on Windows XP when using Facebook Connect (and other sites with similar certificate signing modes).
* Fixed crash that occurs if user unfocuses a textbox during an IME composition.
* Fixed crash with very large strings of `WebConfig.UserScript`.

#### Awesomium.Core

* Fixed issue in `ResourceDataSource` (folders with a dash are replaced by an underscore).
* Assigning null string to `JSValue` doesn't set `JSValue.IsNull`. ([#19](https://github.com/awesomium/awesomium-pub/issues/19))
* Fixed invalid synchronization context issues that may occur on *UpdateTimerCallback*, when `UpdateState` is `InAutoUpdate`. ([#48](https://github.com/awesomium/awesomium-pub/issues/48))
* Fixed illegal disposal of the managed wrapper of the global `Null` and `Undefined` JSValues. ([#53](https://github.com/awesomium/awesomium-pub/issues/53))
* Fixed issues with `IWebView.HTML` not returning the full page contents. ([#61](https://github.com/awesomium/awesomium-pub/issues/61))
* Fixed issue where early *`window.close`* calls where not being processed. ([#61](https://github.com/awesomium/awesomium-pub/issues/61))
* Using invoke on several threads crashes Awesomium. ([#59](https://github.com/awesomium/awesomium-pub/issues/59))
* Fixed issues with `JSObject` dynamic indexer calls on **VB.NET**.
* Fixed issues with `IWebView.SaveImageAt`.
* Fixed issue with `JSObject.ToString` crashing when called on Global objects.
* Fixed issue in `WebSession.HasViews` that could cause a `WebSession` being prematurely released.
* Fixed issue where local `JSObject` instances being assigned as members of other local JSObjects, are set by value.
* Fixed massive thread spawning at `InAutoUpdate` mode when UI thread is blocked. ([#71](https://github.com/awesomium/awesomium-pub/issues/71))
* Fixed issue where `SurfaceFactory` would fail to destroy unused surfaces.
* Fixed issue preventing navigation when only the anchor of a URL is changed. ([#52](https://github.com/awesomium/awesomium-pub/issues/52))

#### Awesomium.Windows.Controls (WPF)

* Fixed `ArgumentException` at `RenderProcess` getter. ([#69](https://github.com/awesomium/awesomium-pub/issues/69))
* Fixed exception that occurred during the initialization of `WebControlCommands`. ([#57](https://github.com/awesomium/awesomium-pub/issues/57))
* Fixed error in `ISynchronizeInvoke.InvokeRequired` implementation.
* Fixed issue with `DataPakSourceProvider.PakPath` validation.
* Fixed issue that would prevent temporarily unloaded `WebControl` containers (such as those in a TabControl's tab), accessing the `WebDialogsLayer` decorator.


## Changes in Samples

* Updated all samples to use a standard `WebCore.Shutdown` policy.
* Fixes and improvements in **_BasicAsyncSample_** to use latest features.
* Updated **_JavascriptSample_** to reflect the new **[JEC](../javascript/jec.html)** features and new **[DLR](../javascript/dlr.html)** support features.
* Updated **_WinFormsSample_** demonstrating asynchronously loading resources through [ResourceInterceptor+DataSource]().
* Updated **_TabbedWPFSample_** demonstrating how to extend **[JIF](../javascript/jif.html)**.
* Updated WPF **_WebControlSample_** to reflect the new **[JEC](../javascript/jec.html)** features and new **[DLR](../javascript/dlr.html)** support features.
* Updated **_WPFJavascriptSample_** to reflect the new **[JEC](../javascript/jec.html)** features and new **[DLR](../javascript/dlr.html)** support features.
* Expanded WPF **_StarterSample_** and **_VBStarterSample_** with examples of taking screenshot and interacting with the page.
* Updated WPF **_StarterSample_** and **_VBStarterSample_** to reflect the new **[JEC](../javascript/jec.html)** features and new **[DLR](../javascript/dlr.html)** support features.


## Known Issues

* On **Windows 8**, **WebGL** is currently not supported.
* **Multi-touch** support for WPF `WebControl` on **Windows 7** and earlier, lacks the *press-and-hold* feature. This is a system limitation. However, events are still propagated to the mouse if you keep your finger to the surface until the system's *press-and-hold* gesture completes.
* Manipulating **scrollbars** when you are using a **touch** surface or stylus with the WPF `WebControl`, is currently handled as regular touch manipulation and may have the oposite from the expected effect. To manipulate scrollbars with a touch point as you would with a mouse, use the *press-and-hold* gesture. (You can still however scroll pages using normal touch gestures, inside the page.)


### Under production:

* Design-time support of the WPF `WebSessionProvider` currently does not allow editing the `DataSources` list directly through the respective dialog but this feature is planned for next release. 
* When you are using the OSX `OSMWebView`, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF `WebControl` and the Windows Forms `WebControl` but not to the MonoMac `OSMWebView` yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.


## Older Changelogs

You can find this and previous Changelogs, under the [Changelogs](http://wiki.awesomium.net/changelogs/) category.
