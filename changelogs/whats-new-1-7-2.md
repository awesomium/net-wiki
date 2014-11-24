---
layout: page
title : What's New in 1.7.2
group: Changelogs
weight: 5

---

We are very proud to announce the release of **Awesomium.NET 1.7.2**. This is a critical update with a number of new features and enhancements.

* [Download the Awesomium 1.7.2 SDK](http://www.awesomium.com/download) (includes the **Awesomium.NET** binaries and samples)
* [Awesomium.NET 1.7.2 API Reference](http://docs.awesomium.net)
* [Setting up on Windows](http://wiki.awesomium.net/getting-started/setting-up-on-windows.html)

A lot has gone into this release. Before you go through the changes in Awesomium.NET, take a look at some major new features:

### Support for Mono

The core assembly has undergone many changes to be platform independent. Our first official **Mono** and **MonoMac** builds are available with this release. 

Starting with 1.7.2, the Windows Core assembly and the **_Awesomium.Mono_** assembly expose exactly the same API and features. Applications using only core features of Awesomium (anything currently available through the *Awesomium.Core* assembly), will only have to change references and packaging to distribute to Mac or Linux. Not a single line of code needs to be changed.

### Support for Cocoa (OSX)

We are excited with the completion and release of **Awesomium for MonoMac** that allows you to build native Cocoa applications on OSX using **Mono** and **Xamarin Studio**.

A new assembly (*Awesomium.Mono.Mac*) that comes with the **Awesomium SDK for Mac OSX**, includes our first **MonoMac** component, [`OSMWebView`](http://docs.awesomium.net/monomac/?tc=T_Awesomium_Mono_Mac_OSMWebView), as well as other components and utilities for creating Cocoa applications on OSX using **C#** and the **Mono** runtime.

For details, read the following articles:

* [Setting Up on Mac OSX](http://wiki.awesomium.net/getting-started/setting-up-on-mac-osx.html)
* [Using the MonoMac OSMWebView](http://wiki.awesomium.net/monomac/using-the-osmwebview.html)

### Support for Unity

Our new **Unity plugin** is ready and available with this release. The plugin includes a new, completely revamped component, [`WebUIComponent`](), design-time support and many new features.

**For details, read the [Using the Unity WebUIComponent](http://wiki.awesomium.net/unity/webuicomponent.html) article.**

### New Features

* Crashed views can be brought back to life by calling any of the `Back`, `Forward`, `Reload` or by setting a new `Source`.
* The `ReduceMemoryUsageOnNavigation` configuration setting and `IWebView.ReduceMemoryUsage` method have been added and can significantly reduce memory usage (at the cost of performance).
* An `IWebView.SelectionComplete` event has been added that is fired when user selection in a page, is completed.
* Our JavaScript Interoperation Framework (JIF) has been redesigned and its size has been reduced (improving performance and functionality for `window.open`, `window.close`, content selection and JavaScript dialogs).
* No property or event arguments that provide a `Uri` will ever return a null reference. Instead, a blank URI (`about:blank`) is now returned that can be checked with the new `IsBlank` `Uri` extension.
* A predefined, default drop-down (popup) menu has been added to the to the Windows Forms `WebControl` (`WebPopupMenu`).

### API Changes

#### New API:

##### *Awesomium.Core*

* [`WebCore.Log`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_WebCore_Log)
* [`LogSeverity`](http://docs.awesomium.net/?tc=T_Awesomium_Core_LogSeverity) enumeration.
* [`WebConfig.ReduceMemoryUsageOnNavigation`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebConfig_ReduceMemoryUsageOnNavigation)
* [`WebSession.ClearCache`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebSession_ClearCache)
* [`IWebView.ReduceMemoryUsage`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_ReduceMemoryUsage)
* [`IWebView.SelectionComplete`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_SelectionComplete)
* [`Utilities.MinifyJavascript`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Utilities_MinifyJavascript)
* [`Uri.IsBlank`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Utilities_IsBlank) (`Uri` extension)
* [`WebMenuItem.Empty`](http://docs.awesomium.net/?tc=F_Awesomium_Core_WebMenuItem_Empty)
* [`WebMenuItem.IsEmpty`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebMenuItem_IsEmpty)
* [`WebPopupMenuInfo.IsValid`](http://docs.awesomium.net/?tc=P_Awesomium_Core_WebPopupMenuInfo_IsValid)

##### *Awesomium.Windows.Controls* (WPF)

* [`UrlConverter`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Controls_UrlConverter) (`IValueConverter`)

##### *Awesomium.Windows.Forms*

* [`WebControl.ProcessInput`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Forms_WebControl_ProcessInput)
* [`WebPopupMenu`](http://docs.awesomium.net/?tc=T_Awesomium_Windows_Forms_WebPopupMenu)

#### Modified API

##### *Awesomium.Windows.Controls* (WPF)

* [`WebViewPresenter.DeviceTransform`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebViewPresenter_DeviceTransform)

### Bug Fixes

* Fixed issue introduced in 1.7.1 that would cause the internal queued navigation of child views created at `IWebView.ShowCreatedWebView` (result of JavaScript 'window.open') be aborted, breaking opener<->child relationship.
* Fixed an "out of sync" exception that could occur at `WebCore` shutdown (during `DestroyUnwrappedViews`).
* Fixed a concurrency issue that could throw an `ArgumentException` (Source array was not long enough) while adding messages in the `WebCore`'s internal asynchronous queue.
* (JavaScript) Fixed "awejif is not defined" that occurred in several occasions.

##### WPF

* Fixed memory leak when disposing of a WPF `WebControl`.
* The WPF `WebControl` (and `WebViewHost`) now properly process all keys (like TAB, Arrows etc), when `ViewType` is set to `Window`.
* Fixed a (rare) `NullReferenceException` thrown when several methods of an `IWebViewPresenter` (like `HidePopupMenu`), were being called in the absence of a valid `Application` object (`Application.Current`).
* Fixed an `InvalidOperationException` (Nullable object must have a value) thrown at mouse input in `WebViewPresenter`.

##### Windows Forms

* Fixed memory leak when disposing of a Windows Forms `WebControl`.
* Fixed an issue that caused page content be selected based on mouse movement, when a `WebControlContextMenu` or `WebPopupMenu` is open.

Please [read here](http://www.awesomium.com/ChangeLog.txt), the list of bugs fixed in native Awesomium. Some of them affected Awesomium.NET.


### Changes in Samples

* Improved the favicon acquisition code in C# and VB.NET samples.
* Fixed issues in the TabbedWPFSample that would potentially cause a memory leak.
* Fixed issue in the C# WinFormsSample that would potentially cause a memory leak.
* The TabbedWPFSample demonstrates the usage of the new `WebConfig.ReduceMemoryUsageOnNavigation`.
* The TabbedWPFSample demonstrates the usage of the new `UrlConverter` (to avoid displaying `about:blank` in the status bar).
* TabbedWPFSample and TabbedFormsSample demonstrate recreation of a crashed view (new feature).


### Known Issues

* On **Windows 8**, **WebGL** is currently not supported.

#### Under production:

* When you are using the OSX `OSMWebView`, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF `WebControl` and the Windows Forms `WebControl` but not to the MonoMac `OSMWebView` yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.

### Older Changelogs

You can find this and previous Changelogs, under the [Changelogs](http://wiki.awesomium.net/changelogs/) category.
