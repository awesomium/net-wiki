---
layout: page
title : What's New in 1.7.4
group: Changelogs
weight: 3

---

We are very proud to announce the release of **Awesomium.NET 1.7.4**. This is a major update with many new features and enhancements. It introduces an *auto-update* and **cross-thread synchronization model**, offers new samples, and fixes several critical bugs on multiple platforms.

* [Download the Awesomium 1.7.4 SDK](http://www.awesomium.com/download) (includes the **Awesomium.NET** binaries and samples)
* [Awesomium.NET 1.7.4 API Reference](http://docs.awesomium.net)
* [Setting up on Windows](http://wiki.awesomium.net/getting-started/setting-up-on-windows.html)
* [Setting up on Mac OS X](http://wiki.awesomium.net/getting-started/setting-up-on-mac-osx.html)

> This changelog also includes the fixes and new features introduced with **v1.7.4.2**. For a presentation, see: **[Critical Patch: 1.7.4.2](http://www.awesomium.com/blog/critical-patch-1-7-4-2/)**.


### Major New Features

With this release we are introducing many important new features. Some of them provide solutions to certain long standing limitations and may require changing the way you did things with Awesomium. Here is a short presentation of some of these features:

#### New Synchronization Model

Starting with **Awesomium.NET v1.7.4**, we have designed and applied a new, awesomium-specific synchronization model and we have utilized available .NET tools to the maximum, allowing *auto-update* in any scenario and **cross-thread** communucation with Awesomium.

**For details, read: [Synchronization Model](../general-use/synchronization-model.html)**

#### WPF Revisited

Our WPF components had some special treatment in this version; features were added, issues and potential leaks were fixed, performance was improved and WPF samples got a *facelift*.

**For details, see:**

* [Using the WPF WebControl](../wpf/webcontrol.html)
* [Using the WebDialogsLayer](../wpf/webdialogslayer.html)
* [Using the SourceBinding](../wpf/sourcebinding.html)
* [Customizing the WebControlContextMenu]()
* [Localizing predefined UI](../wpf/localization.html)
* [Synchronization Model](../general-use/synchronization-model.html)

And of course, don't forget to download our completely revamped **ClickOnce WPF demo**, from **[here](http://awesomium.amadeusoft.com/wpf/setup.exe)**.

#### DataSources Redesigned

Both the [`ResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_ResourceRequest) passed to [`IResourceInterceptor.OnRequest`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IResourceInterceptor_OnRequest) as well as [`DataSourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Data_DataSourceRequest), now implement the new [`IResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceRequest) interface. Through this, DataSources now get valuable information about the request such whether it is `POST` or `GET`, information about the view that sends the request etc.

Also, the new [`AssetProtocol`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_AssetProtocol) configuration setting, allows to specify a custom protocol to be used with DataSources, other than the default **_`asset`_**.

**For details, see: [Using DataSources](../general-use/using-data-sources.html)**.

#### Awesomium for Xamarin Mac (XamMac)

With the **Awesomium SDK for Mac OSX**, we now provide the **Awesomium.Xamarin.Mac** assembly, targeting Xamarin's **XamMac**. Clients of **Xamarin** can use this to create applications using **Awesomium** that embed **Mono** and can be distributed to the **Apple Store**.

**For details, read:**

* [Using the OSMWebView](../monomac/osmwebview.html)
* [Setting up on Mac OS X](http://wiki.awesomium.net/getting-started/setting-up-on-mac-osx.html)


### New Features

##### Core

* Created a synchronization model with internal `SynchronizationContext`, `ISynchronizeInvoke` implementation by views, [`WebCore.QueueWork`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_WebCore_QueueWork) and [`WebCore.Run`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Run) that allows cross-thread interaction with Awesomium.
* Added support for creating **custom** (native or managed) **child process**.
* Made it so unwrapped native child views are properly destroyed when [`ShowCreatedWebView`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView) is not handled.
* Added the ability access the current rendering (child) `Process` through [`IWebView.RenderProcess`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_RenderProcess).
* Added support for specifying a different protocol for `DataSource` (other then *`asset`*).
* You can now easily revive from `Crashed` by `GoBack`, `GoForward`, `GoHome` and `Reload`.
* Applied new thread-affinity insurance model. Users can bypass with [`ThreadAffinityEnsuredAttribute`](http://docs.awesomium.net/?tc=T_Awesomium_Core_ThreadAffinityEnsuredAttribute).
* Made it so the child process's priority is lowered when the view is not rendering.
* Redesigned our `JSObject` references model.
* Added support for specifying a **_catch-all_** `DataSource` for handling requests to any asset host.
* DataSourceRequests now implement the new [`IResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceRequest) providing the [`OnRequest`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Data_DataSource_OnRequest) call with important information about the request.
* Added support for downloading an image at specified coordinates in a page, with [`IWebView.SaveImageAt`]().
* Added commands relevant to the new [`SaveImageAt`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_SaveImageAt), to predefined context-menus.
* Made it so users can remove specific items from `WebCore.Downloads`.
* Added support for acquiring the HTML of the currently loaded page through [`IWebView.HTML`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_HTML).
* Added design-time support for **Visual Studio 2013**.
* Significantly improved and added more, documentation.

##### WPF and Windows Forms

* Made it so that the address in a WPF `TextBox` bound with [`SourceBinding`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Data_SourceBinding) or in a WinForms [`AddressBox`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Forms_AddressBox), is not updated when they have keyboard focus.
* Improved performance of the WPF `WebControl` by utilizing features of the new synchronization model.
* Added the WPF [`WebDialogsLayer`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebDialogsLayer) decorator for presenting JS dialogs.
* Redesigned WPF JS dialogs.
* Added full localization support for WPF context menus and commands.
* Made it so [`SourceBinding`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Data_SourceBinding) can target any type with a `Source` `Uri` property.
* Users can now provide custom handling of String->Uri conversion when using a [`SourceBinding`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Data_SourceBinding), with a custom [`UriValueConverter`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Data_UriValueConverter).
* Improved appearance of WPF `WebPopupMenu`.
* Improved `WebViewPresenter` rendering in high DPI.
* Added support for small and extra large icons in WPF [`DownloadItem`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_DownloadItem).
* Added default "awe" and "data" XAML namespace prefixes.
* Made it so users can customize the "crashed" layer (*Sad Tab*) and all relevant predefined UI.

**[1.7.4.2]:**

* Improved WPF `WebControl` rendering quality in non-standard DPI settings.
* Added the ability to choose if you want the WPF `WebControl` to automatically adjust to higher DPI settings.

##### Unity

* Updated Unity sample scripts.

##### MonoMac/Xamarin

* Created the **[`Awesomium.Xamarin.Mac`]()** assembly, targeting **XamMac**, to be used by **Xamarin** clients for producing applications that can be distributed to the **Apple Store**.
* Created **OsmMonoMac** assembly (our build of MonoMac). This replaces the previous Awesomium.MonoMac assembly (with ambiguous name).


### API Changes

#### New API:

##### Awesomium.Core

* [`WebCore.Run`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Run)
* [`WebCore.UpdateState`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_UpdateState)
* [`WebCore.QueueWork`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_WebCore_QueueWork)
* [`WebCore.IsInitialized`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_IsInitialized)
* [`WebCore.IsChildProcess`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_IsChildProcess)
* [`WebCore.ChildProcessMain`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_ChildProcessMain)
* [`WebConfig.AssetProtocol`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_AssetProtocol)
* [`WebConfig.DEBUGGING_HOST_DEFAULT`](http://docs.awesomium.net/?tc=F_Awesomium_Core_WebConfig_DEBUGGING_HOST_DEFAULT)
* [`WebCoreUpdateState`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebCoreUpdateState)
* [`IWebView.SaveImageAt`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_SaveImageAt)
* [`IWebView.HTML`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_HTML)
* [`IWebView.RenderProcess`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_RenderProcess)
* [`WebSession.ReservedAssetHosts`]()
* [`IResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceRequest)
* [`Utilities.ToWebRequest`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_Utilities_ToWebRequest) ([`IResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IResourceRequest) extension)
* [`DataSource.CATCH_ALL`](http://docs.awesomium.net/?tc=F_Awesomium_Core_Data_DataSource_CATCH_ALL)
* [`DownloadCollection.Remove`](http://docs.awesomium.net/?tc=M_Awesomium_Core_DownloadCollection_Remove)
* [`DownloadItem.CanRemove`](http://docs.awesomium.net/?tc=M_Awesomium_Core_DownloadItem_CanRemove)
* [`DownloadItem.Remove`](http://docs.awesomium.net/?tc=M_Awesomium_Core_DownloadItem_Remove)
* [`DownloadItem.OriginViewId`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DownloadItem_OriginViewId)
* [`ThreadAffinityEnsuredAttribute`](http://docs.awesomium.net/?tc=T_Awesomium_Core_ThreadAffinityEnsuredAttribute)

**[1.7.4.2]:**

* [`JSWindowOpenSpecs.Status`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSWindowOpenSpecs_Status)
* [`ShowCreatedWebViewEventArgs.HasUserSpecs`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_HasUserSpecs)

##### Awesomium.Windows.Controls (WPF)

* [`WebDialogsLayer`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebDialogsLayer)
* [`WebDialogsStyle`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_WebDialogsStyle)
* [`SourceBinding.Converter`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Data_SourceBinding_Converter)
* [`SourceBinding.ConverterParameter`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Data_SourceBinding_ConverterParameter)
* [`WebControlCommands.SaveImageAs`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebControlCommands_SaveImageAs)

**[1.7.4.2]:**

* [`WebControl.AdjustToDpi`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebControl_AdjustToDpi)

#### Modified API:

##### Awesomium.Core

* [`IWebView+ISynchronizeInvoke`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IWebView)
* [`ViewModel.OnPropertyChanged`](http://docs.awesomium.net/?tc=M_Awesomium_Core_ViewModel_OnPropertyChanged) (removed sender)
* [`DataSourceRequest+IResourceRequest`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Data_DataSourceRequest)

#### Obsolete API:

##### Awesomium.Core

* [`WebCore.IsRunning`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_IsRunning)
* [`WebCore.Update`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Update)
* [`WebCore.IsAutoUpdateEnabled`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebCore_IsAutoUpdateEnabled)


### Bug Fixes

##### Native Awesomium

* Fixed issue that would cause a crash when `WebPreferences.Javascript` was set to *`false`*. ([#12](https://github.com/awesomium/awesomium-pub/issues/12))
* Fixed issue with about:blank being added to history at beginning of view's lifetime. ([#30](https://github.com/awesomium/awesomium-pub/issues/30))
* Fixed issue with PDF files not being downloaded when Adobe Reader is installed. ([#11](https://github.com/awesomium/awesomium-pub/issues/11))
* Fixed crash that occurs when `JSObject.Invoke` fails on executing a callback with invalid JavaScript.
* Fixed crash issue with *`requestQuota`*.
* Fixed crash when users hit `CTRL+LEFT` at beginning of line.

**[1.7.4.2]:**

* Fixed crash when `WebPreferences.WebGL` is enabled.
* Fixed crash when `WebPreferences.EnableGPUAcceleration` is enabled.

##### Awesomium.Core

* Fixed occasional `NullReferenceException` in `WebCore`'s static constructor which causes a `TypeInitializationException` and crash. ([#33](https://github.com/awesomium/awesomium-pub/issues/33))
* Fixed issues in JIF that would cause `IWebView.Selection` to not update. ([#24](https://github.com/awesomium/awesomium-pub/issues/24))
* Made it so `IWebView.Selection` contents are cleared upon navigation. ([#34](https://github.com/awesomium/awesomium-pub/issues/34))
* Fixed issues with reviving from `Crashed`.
* Fixed issue that would cause requests from views with invalid/special identifier (0), be canceled. This affected login procedure in certain sites. [#38](https://github.com/awesomium/awesomium-pub/issues/38)
* Fixed issue that would cause a `JSObject` instance be prematurely released.
* Fixed issue that would cause `ResourceDataSource` not be able to access resources when there are numbers in the path. ([#23](https://github.com/awesomium/awesomium-pub/issues/23))
* Fixed some bugs in `DirectoryDataSource` ([#39](https://github.com/awesomium/awesomium-pub/issues/39))
* Made it so `DocumentReady` is fired on *`window.open`* child views ([#25](https://github.com/awesomium/awesomium-pub/issues/25))
* Made it so full-screen Flash windows, do not appear to the background of the application when using `Offscreen` views. ([#37](https://github.com/awesomium/awesomium-pub/issues/37))
* Fixed issue with `Zoom` not being applied to newly created views navigating to a zoomed host.

**[1.7.4.2]:**

* Fixed occassional `ArgumentNullException` when trying to p/invoke on native Awesomium.
* Fixed issue that would cause an exceptional WebCore shutdown, when there were duplicate values in the specs of a JavaScript `window.open` call. ([#46](https://github.com/awesomium/awesomium-pub/issues/46))
* Fixed `OperationCanceledException` on `WebCore.Run`. ([#45](https://github.com/awesomium/awesomium-pub/issues/45))
* Fixed `ArgumentNullException` on internal `WebCore.Log` calls. ([#44](https://github.com/awesomium/awesomium-pub/issues/44))
* Fixed issue that would cause a crash when the specified child process was not found.

##### Awesomium.Windows.Controls (WPF)

* Fixed incorrect rendering when scrolling iFrames with managed `Offscreen` views. ([#22](https://github.com/awesomium/awesomium-pub/issues/22))
* Fixed issue with `WebControlCommands` handling keyboard shortcuts already handled by native Awesomium.
* `WebControl` no longer scrolls when a drop-down (popup) menu is open.
* Made it so `WebViewHost` (windowed WebControls) properly handles Modifier + Key. ([#29](https://github.com/awesomium/awesomium-pub/issues/29))
* Made it so tool-tips and drop-down (popup) menus in WPF `WebControl`, close when the container `Window` is moved. ([#36](https://github.com/awesomium/awesomium-pub/issues/36))
* Made it so that text in WPF `WebControl` tool-tips, is properly wrapped. ([#20](https://github.com/awesomium/awesomium-pub/issues/20))
* Few fixes and improvements that prevent memory leaks on WPF.
* Made it so that buttons in WPF dialogs properly handle mouse clicks.

**[1.7.4.2]:**

* Fixed issue that would cause significant memory growth while scrolling up and down a WPF `WebControl`. ([#43](https://github.com/awesomium/awesomium-pub/issues/43))
* Fixed rare issue with WPF `WebControl` displaying blank content until resized.

##### Awesomium.Windows.Forms (Windows Forms)

* Fixed incorrect rendering when scrolling iFrames with managed `Offscreen` views. ([#22](https://github.com/awesomium/awesomium-pub/issues/22))
* Made it so users can handle key-related events and override default handling, on WinForms `Offscreen` `WebControl`. ([#35](https://github.com/awesomium/awesomium-pub/issues/35))

##### Awesomium.Mono.Mac (MonoMac/Xamarin)

* Fixed issues that would cause a crash when a new `OSMWebView` is created, with newer versions of MonoMac and XamMac.


### Changes in Samples

* Fixed issue with `UpdateFavicon` code in samples, that would cause a `WebCore` shutdown.
* Added new **_BasicAsyncSample_** that demonstrates all features of the new synchronization model.
* Redesigned **_BasicSample_** to use the new synchronization model.
* Added **_Awesomium_** (C++) sample; a custom native child process.
* Added **_CustomProcess_** (C#) sample; a custom managed child process.
* Upgraded **Awesomium.Unity** sample project to Unity 4.3.x.
* Updated all WPF samples to reflect changes and new features.
* Completely redesigned the **_TabbedWPFSample_** using fresh Metro look and feel.
* Added a C# and VB.NET WPF **_StarterSample_**.
* Removed `Microsoft.Widows.Shell` dependency from WPF samples.
* Added **XamMac** (equivalent to the MonoMac) samples, for use by **Xamarin** clients.


### Known Issues

* On **Windows 8**, **WebGL** is currently not supported.


#### Under production:

* When you are using the OSX `OSMWebView`, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF `WebControl` and the Windows Forms `WebControl` but not to the MonoMac `OSMWebView` yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.


### Older Changelogs

You can find this and previous Changelogs, under the [Changelogs](http://wiki.awesomium.net/changelogs/) category.
