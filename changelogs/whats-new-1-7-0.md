---
layout: page
title : What's New in 1.7.0
group: Changelogs
weight: 7

---

Before you go through the changes in Awesomium.NET, take a look at the major changes in native Awesomium:

* [Awesomium 1.7.0 Final Release](http://labs.awesomium.com/whats-new-in-1-7-0/)

Here is a summary of changes in **Awesomium.NET**:

### New Features

* Added protections that prevent JS interoperation methods being called on an `IWebView` instance, before `DocumentReady`.
* Added protections that prevent the use of JS-related API, when `WebPreferences.Javascript` is disabled.
* `JSObject` no longer inherits `DynamicObject`. It now provides advanced low level dynamic API support through .NET's `IDynamicMetaObjectProvider`.
* Added protections that prevent the use of synchronous JS API calls, when the context is synchronous (from within a synchronous custom method handler).
* Implemented internal downloads management and added the `DownloadCollection` and `DownloadItem` classes that allow you to easily monitor and bind your MVVM application to download operations.
* Added support for Zoom setting to views and the ability to retrieve settings for specific hosts from WebSession.
* Made it so the WPF/WinForms WebSessionProviders provide a previously created WebSession with the same DataPath, instead of attempting to create a new one.
* Made it so the IsManipulationEnabled of WPF presenters is bound to the WebControl (this will propagate the ManipulationDelta routed event to the WebControl).
* Added design-time support for WebPreferences (available through WebSessionProviders).
* All properties and event args that accept or return a Uri, are now ready to silently respond to malformed URIs.
* Removed the ResourceInterceptor class. IResourceInterceptor interface remains.
* Added support for JavaScript dialogs.
* Added support for handling Certificate errors and obtaining page's security related information.
* Added the ability cancel/handle navigations asynchronously, through the new INavigationInterceptor service. The service also supports white/blacklisting.
* Added default dialog for selecting a folder at Print requests, SelectLocalFiles and design-time, to the WPF WebControl and design-time support.
* Added default drop-down (popup) menu to WPF WebControl (see WebPopupMenu and WebPopupMenuBase).
* Added default WPF certificate error dialog.
* Added default WPF web page info cpntrol and popup. (see WebPageInfoPopup and WebPageInfoControl).
* Added default dialog layers for handling Javascript dialogs to the WPF WebControl.
* Added the ability to handle all user input (including system and control keyboard events) through the WPF WebControl's Preview events.
* Added default dialogs for handling Javascript dialogs to the Windows Forms WebControl.
* Added default ContextMenu to the Windows Forms WebControl.
* Extended design-time support for the WPF WebControl.
* Added design-time support for the Windows Forms WebControl.

### API Changes

#### New API:

##### *Awesomium.Core*

* [`WebCore.Configuration`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebCore_Configuration): Returns the configuration settings used to initialize the core.
* [`WebCore.Download`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_WebCore_Download) 
* [`WebSession.ClearCookies`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebSession_ClearCookies): Clears all cookies for this session, asynchronously.
* [`WebSession.IsJavascriptEnabled`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebSession_IsJavascriptEnabled): No JS related API can be called if this is `false`.
* [`WebSession.GetZoomForURL`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebSession_GetZoomForURL): Gets the zoom setting for a particular host.
* [`WebSessionCollection.Item`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebSessionCollection_Item): Indexer to access sessions by data path.
* [`WebSessionCollection.Contains`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebSessionCollection_Contains): Overload that gets if a `WebSession` synchronizing to the specified *dataPath*, currently exists.
* [`NativeViewModel`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_NativeViewModel): Split from [`ViewModel`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_ViewModel). Separates disposal logic from MVVM logic so that `ViewModel` can be used by non-disposable types.
* [`NetError`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_NetError) enumeration: Provides description for the error code reported during a `LoadingFrameFailed` event.
* [`CertError`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_CertError): Certificate errors enumeration.
* [`JavascriptAsynchMethodEventHandler`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_JavascriptAsynchMethodEventHandler): Delegate used in *dynamic* programming to add asynchronous custom methods to a `JSObject`.
* [`DownloadItem`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_DownloadItem): MVVM friendly info for a dowanload operation.
* [`DownloadCollection`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_DownloadCollection): A collection of maintained download operations.
* [`WebCore.DownloadBegin`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_WebCore_DownloadBegin): Fired before a download starts; provides the DownloadItem just added to Downloads.
* [`WebCore.Downloads`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebCore_Downloads): List of active, complete or canceled download operations.
* [`INavigationInterceptor`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_INavigationInterceptor): Service available on IWebView instances. Allows you to handle navigation requests or add filtering ruls - whitelist, blacklist.
* [`IWebView.WindowClose`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_WindowClose): Event fired when `window.close` is called from JavaScript.
* [`JSDialogFlags`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_JSDialogFlags): Used with [`ShowJavascriptDialog`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowJavascriptDialog).
* [`IWebView.ShowJavascriptDialog`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowJavascriptDialog) (alert, confirm, prompt)
* [`IWebView.ConsoleMessage`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ConsoleMessage): Event fired for Javascript console messages.
* [`IWebView.InitializeView`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_InitializeView): Event fired right before the underlying web-view of a technology specfic WebControl, is instantiated.
* [`IWebView.NativeViewInitialized`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_NativeViewInitialized): Event fired right after the underlying web-view of a technology specfic WebControl, is instantiated.
* [`IWebView.SynchronousMessageTimout`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_SynchronousMessageTimout): Sets the maximum amount of time to wait for a response from a synchronous IPC message dispatched to the view's renderer process.
* [`IWebView.Zoom`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Zoom): Sets zoom level.
* [`IWebView.ZoomIn`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_IWebView_ZoomIn): Zooms in by 20%.
* [`IWebView.ZoomOut`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_IWebView_ZoomOut): Zooms out by 20%.
* [`IWebView.ResetZoom`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_IWebView_ResetZoom): Resets to 100%.
* [`IWebView.Printing`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_Printing): Event for when a printing operation starts.
* [`IWebView.IsPrinting`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_IsPrinting): Property reflecting printing operation status.
* [`IWebView.RequestPageInfo`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_IWebView_RequestPageInfo): Requests page's security related information.
* [`IWebView.ShowPageInfo`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowPageInfo): Event fired in response to [`RequestPageInfo`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_IWebView_RequestPageInfo).
* [`IWebView.Presenter`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Presenter) (See [`IWebViewPresenter`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Presenter))
* [`WebPageInfo`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_WebPageInfo): Available through [`ShowPageInfo`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowPageInfo).
* [`WebPopupMenuInfo.Item`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebPopupMenuInfo_Item): Indexer; [`WebPopupMenuInfo`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_WebPopupMenuInfo) is now `IEnumerable<WebMenuItem>`.
* [`WebPopupMenuInfo.Count`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebPopupMenuInfo_Count)
* [`IWebView.CertificateError`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_CertificateError): Event fired on certificate errors. Allows you to ignore errors.
* [`IWebView.Identifier`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Identifier): Unique, global web-view identifier.
* [`WebViewCollection.GetByProcessId`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebViewCollection_GetByProcessId)
* [`WebViewCollection.GetById`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebViewCollection_GetById) (See `IWebView.Identifier`)
* [`WebViewCollection.Contains`](http://docs.awesomium.net/1_7_0/?tc=M_Awesomium_Core_WebViewCollection_Contains): Overload for seeking by unique identifier (see [`IWebView.Identifier`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Identifier)).
* [`UrlEventArgs.HasErrors`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_UrlEventArgs_HasErrors): Indicates that the original URI provided, is malformed.
* [`UrlEventArgs.OriginalString`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_UrlEventArgs_OriginalString): The original URI string.
* [`JSWindowOpenSpecs`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_JSWindowOpenSpecs): Structure; represents the specs specified by users in a JavaScript `window.open` call, including size and positioning, if any. Available at [`ShowCreatedWebView`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView).
* [`CreatedWebViewEventArgs.IsWindowOpen`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_IsWindowOpen) (See [`ShowCreatedWebView`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView))
* [`CreatedWebViewEventArgs.IsUserSpecsOnly`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_IsUserSpecsOnly) (See [`ShowCreatedWebView`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView))
* [`CreatedWebViewEventArgs.Specs`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_Specs) (See [`JSWindowOpenSpecs`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_JSWindowOpenSpecs))
* [`IWebView.IsJavascriptEnabled`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_IsJavascriptEnabled): Prevents calling Javascript-related API when [`WebPreferences.Javascript`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_WebPreferences_Javascript) is `false`.
* [`CoreShutdownEventArgs.Exception`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_CoreShutdownEventArgs_Exception): Gets the unhandled exception that has caused an unwanted `WebCore` shutdown.

##### *Awesomium.Windows.Controls* (WPF)

* [`WebPopupMenu`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Controls_WebPopupMenu): Default drop-down, popup menu.
* [`WebControl.PopupMenuResourceKey`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Windows_Controls_WebControl_PopupMenuResourceKey): `ComponentResourceKey` for the default popup menu.
* [`SourceBinding`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Data_SourceBinding): `MarkupExtension`; binds a WPF `TextBox` to [`WebControl.Source`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Windows_Controls_WebControl_Source) and provides features to make it act as an *address-box*.
* [`UriValueConverter`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Data_UriValueConverter) `IValueConverter` for `Uri`.
* [`InputEventManager`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_InputEventManager): `WeakEventManager` that allows to use the "weak event pattern" for `UIElement` user input events; used by the [`SourceBinding`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Data_SourceBinding).
* [`InputEventType`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_InputEventType): Used with [`InputEventManager`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_InputEventManager) (see above).
* [`WebViewEventManager`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_WebViewEventManager): `WeakEventManager` that allows to use the "weak event pattern" for `IWebView` events.

##### *Awesomium.Windows.Forms* (Windows Forms)

* [`WebControl.ViewType`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Windows_Forms_WebControl_ViewType): Property that tells the Windows Forms WebControl to optionally wrap an offscreen web-view. Also allows the use of transparency.
* [`WebControl.IsTransparent`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Windows_Forms_WebControl_IsTransparent): See above.
* [`WebSessionProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_WebSessionProvider): Component; equivalent to the WPF `WebSessionProvider`, allows you to assign a `WebSession` to a Windows Forms `WebControl` at design-time.
* [`DataSourceProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_DataSourceProvider): Base class for Windows Forms [`DataSource`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_Data_DataSource) providers. Used with [`WebSessionProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_WebSessionProvider).
* [`DataSourceProviderCollection`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_DataSourceProviderCollection): Collection of [`DataSourceProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_DataSourceProvider) instances maintaineded by a [`WebSessionProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_WebSessionProvider).
* [`DataPakSourceProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_DataPakSourceProvider)
* [`DirectoryDataSourceProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_DirectoryDataSourceProvider)
* [`ResourceDataSourceProvider`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Windows_Forms_ResourceDataSourceProvider)

#### Modified API

* All API members' names with an *ID* suffix, have been renamed to have an *Id* suffix (eg, `IWebView.ProcessID` -> `IWebView.ProcessId`). These would be too many to list here.
* Changed all event triggers to not accept a *sender* (having a *sender* was a wrong pattern).
* `IWebView.Download` -> [`WebCore.Download`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_WebCore_Download)
* Removed `IWebView.BeginNavigation` (replaced with advanced [`INavigationInterceptor`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_INavigationInterceptor) service).
* Removed `WebPopupMenuInfo.Items` ([`WebPopupMenuInfo`](http://docs.awesomium.net/1_7_0/?tc=T_Awesomium_Core_WebPopupMenuInfo) is now `IEnumerable<WebMenuItem>`)
* Moved `ShowCreatedWebViewEventArgs.InitialPos` to [`JSWindowOpenSpecs.InitialPosition`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_JSWindowOpenSpecs_InitialPosition).
* Moved [`IWebViewPresenter`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Presenter) to [`Awesomium.Core`](http://docs.awesomium.net/1_7_0/?tc=N_Awesomium_Core). Now all views can have a presenter assigned (see [`IWebView.Presenter`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Core_IWebView_Presenter)) to handle major UI-related events (still implemented by WPF presenters).

### Bug Fixes

* Fixed issue with `ResourceDataSource` that would not allow requests with queries.
* Fixed issue in designer of the Windows Forms and WPF `WebControl`, that would cause the core to attempt to make p/invoke calls to the Awesomium library at design-time.
* Fixed bug that would prevent escaped URIs from being properly passed to servers.
* Fixed bug that would cause the internal `SurfaceFactory` to throw an exception, when destroying a `Surface`.
* Fixed bug in default implementation of dialog callbacks in `WebView`. Events are now properly canceled.
* Removed all useless production-time `GC.Collect()` calls.
* Made it so default navigation of new child views, is properly cancelled until the view is wrapped.
* Removed useless protection from `CreateGlobalJavascriptObject`, that would prevent the method from being called before `ProcessCreated`. This is no longer a requirement.
* Fixed bug that would cause an exception be thrown at user input, when using the WPF `WebViewPresenter` independently.
* Fixed issues with setting the [`NativeView`](http://docs.awesomium.net/1_7_0/?tc=P_Awesomium_Windows_Controls_WebControl_NativeView) property on the WPF `WebControl`.
* Fixed false `LoadingFrameFailed` events occuring before downloads.
* Fixed bug that would prevent setting the `Source` property to the WPF `WebControl`, at runtime.
* Fixed bug that would prevent the `Source` property of the WPF `WebControl`, to reflect the currently loaded page URL.
* Fixed bug introduced in RC3 that would prevent tranparency to the WPF `WebControl`.
* Fixed `AddressChanged` event not being fired on WPF `WebControl`.
* Fixed issues with Unicode strings not being properly passed to and from native Awesomium.
* Fixed issues with the `IsCrashed` property not reflecting the actual status of the view, and issues with the `Crashed` event not being fired.
* Fixed exception that could occur when attempting to `Shutdown` the core during internal update.
* Fixed bug that could occassionally cause a `NullReferenceException` at `ExecuteJavascript`.
* Fixed `InvalidCastException` when setting `ViewType` on the WPF `WebControl`.
* Fixed issue in WPF `WebControl` that caused the right side to be cropped when DPI was set to any value other than the default.
* Fixed view crash when `WebPreferences.Javascript` was set to `false` on the view's `WebSession`.
* Fixed bug causing a WPF `WebControl` to crash when repeatedly opening/closing a `WebPopupMenu`.
* Fixed *"Width and Height must be positive"* exceptions on the WPF `WebControl`.
* Fixed focusing editable content issues on the WPF `WebControl`.



Please [read here](http://www.awesomium.com/ChangeLog.txt), the list of bugs fixed in native Awesomium. Some of them affected Awesomium.NET.


### Known Issues

* On **Windows 8**, **WebGL** is currently not supported.
* On some **Windows XP** installations, you may get a **DllNotFoundException** when trying to execute an application that uses Awesomium (native or .NET bindings). This indicates you need to install the latest update of **Direct X**, available here: [DirectX End-User Runtimes (June 2010)](http://www.microsoft.com/en-us/download/details.aspx?id=8109). **Note:** If you are using the Windows Installer to deploy the Awesomium SDK, the setup package takes care of this.

#### Under production:

* When you are using the Windows Forms WebControl, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF WebControl but not to the Windows Forms WebControl yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/1_7_0/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.