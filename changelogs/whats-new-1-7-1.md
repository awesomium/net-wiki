---
layout: page
title : What's New in 1.7.1
group: Changelogs
weight: 6

---

We are very proud to announce the release of **Awesomium.NET 1.7.1**. This is a critical update with a number of new features and enhancements but mostly bugfixes that we have applies the past two months, mainly thanks to your feedback.

* [Download the Awesomium 1.7.1 SDK](http://www.awesomium.com/download) (includes the **Awesomium.NET** binaries and samples)
* [Awesomium.NET 1.7.1 API Reference](http://docs.awesomium.net)
* [Setting up on Windows](http://wiki.awesomium.net/getting-started/setting-up-on-windows.html)

A lot has gone into this release. Before you go through the changes in Awesomium.NET, take a look at the major changes in native Awesomium:

* [Awesomium 1.7.1](http://labs.awesomium.com/whats-new-in-1-7-1/)

Here is a summary of changes in **Awesomium.NET**:

### New Features

* Made so you can now create views and WebControls that display the source code of loaded web pages.
* The internal Download Manager has been redesigned so that it respects the initial request that triggered the download operation.
* `JSValue.ToString` now correctly reports the native textual value.
* Internal JavaScript Interoperation Framework has been improved in handling JavaScript dialog requests from frames and displaying the textual representation of JavaScript objects correctly, when such objects are passed to an alert, confirm or prompt.
* Added the ability to make asynchronous method invocations on a `JSObject`, when using dynamic programming.
* Added the ability to acquire a JavaScript object's Function members and subsequently invoke them when using dynamic, even through a variable.
* Added more checks for the validity of the specified outputDirectory, when using `IWebView.PrintToFile`.
* Made it safe to perform certain operations on windowed views before `ParentWindow` is set.
* Made it so certain events fired on created child views, are now captured and passed to the managed wrapper (when `ShowCreatedWebViewEventArgs.NewViewInstance` is wrapped).
* Made it so requests to child views that are the result of links (or forms with method GET) with `target="_blank"`, are canceled by default prior to `ShowCreatedWebView`.

### API Changes

#### New API:

##### *Awesomium.Core*

* [`WebCore.CreateSourceWebView`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_CreateSourceWebView)
* [`IWebView.IsSourceView`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_IsSourceView).
* [`DownloadItem.CurrentSpeed`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DownloadItem_CurrentSpeed)
* [`DownloadEventArgs.ViewId`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DownloadEventArgs_ViewId)
* [`UploadElement.IsEmpty`](http://docs.awesomium.net/?tc=P_Awesomium_Core_UploadElement_IsEmpty) (also added equiality & inequality operators)
* [`ResourceRequest.IsWindowOpen`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ResourceRequest_IsWindowOpen)
* [`ShowCreatedWebViewEventArgs.IsPost`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_IsPost)
* [`ShowCreatedWebViewEventArgs.PostData`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_PostData)
* [`ShowCreatedWebViewEventArgs.IsNavigationCanceled`](http://docs.awesomium.net/?tc=P_Awesomium_Core_ShowCreatedWebViewEventArgs_IsNavigationCanceled)
* [`Utilities.ParseQueryString`](http://docs.awesomium.net/?tc=M_Awesomium_Core_Utilities_ParseQueryString) (Uri extension)

##### *Awesomium.Windows.Controls* (WPF)

* [`DownloadItem.CancelCommand`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_DownloadItem_CancelCommand)

#### Modified API

* `IWebView.SynchronousMessageTimout` -> [`IWebView.SynchronousMessageTimeout`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_SynchronousMessageTimeout) (Corrected typo)
* `implicit operator JSValue[](JSValue)` -> [`explicit operator JSValue[](JSValue)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSValue_op_Explicit) (Needed for proper passing of arguments to JSObject.Invoke)
* `explicit operator bool(JSValue)` -> [`implicit operator bool(JSValue)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSValue_op_Implicit_1) (Allows easily performing validity checks)

### Bug Fixes

* `WebCoreConfig.RemoteDebuggingHost` default value is now correctly set to `127.0.0.1`.
* Fixed issue with downloads failing when triggered from an authentication context.
* Fixed issue with downloads failing when triggered by a POST request.
* Fixed exception thrown at `WebSession.SetCookie`.
* Fixed issue with `ShowCreatedWebViewEventArgs.IsWindowOpen` occasionally returning false for window.open calls triggered from a frame.
* Fixed exception thrown when attempting to create a local `JSObject`.
* Fixed issue with synchronous JavaScript calls timeout, when JavaScript would call back into native code (such as with a modal JavaScript dialog).
* Fixed exception thrown when attempting to use a cloned `JSObject`.
* Fixed issue that could cause `NativeViewInitialized` be fired multiple times.
* Fixed issue that would cause an `AccessViolationException` when multiple views were created fast (exception thrown while I/O and Core threads were simultaneously accessing the core's `WebViewCollection`).
* Fixed unhandled exception thrown while attempting to inject our internal JavaScript Interoperation Framework on an invalidated view.
* Fixed false cancelling default navigation to target URL, on child views created as a result of submitting an HTML form with `target="_blank"` and `method="post"`. (This bug caused the POST data to be lost.)
* Fixed issue causing `UrlEventArgs.HasErrors` to always return true.        
* (WPF) Fixed re-introduced issue with `WebViewPresenter` of `WebControl` rendering in wrong size on systems with non-standard DPI setting.
* (WPF) Fixed issue with `WebViewPresenter` of `WebControl` not scrolling properly on systems with non-standard DPI setting.
* (WPF) Fixed issue with `WebControlContextMenu` of `WebControl` not displayed in the correct position on systems with non-standard DPI setting.
* (WPF) Fixed issue with `WebPageInfoPopup` of `WebControl` not displayed in the correct position on systems with non-standard DPI setting.
* (WPF) Fixed issue that would cause a `WebPopupMenu` close when clicking on its scrollbar.



Please [read here](http://www.awesomium.com/ChangeLog.txt), the list of bugs fixed in native Awesomium. Some of them affected Awesomium.NET.


### Known Issues

* On **Windows 8**, **WebGL** is currently not supported.

#### Under production:

* When you are using the Windows Forms WebControl, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF WebControl but not to the Windows Forms WebControl yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.

### Older Changelogs

You can find this and previous Changelogs, under the [Changelogs](http://wiki.awesomium.net/changelogs/) category.