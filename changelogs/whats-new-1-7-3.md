---
layout: page
title : What's New in 1.7.3
group: Changelogs
weight: 4

---

We are very proud to announce the release of **Awesomium.NET 1.7.3**. This is a critical update with a number of new features and enhancements.

* [Download the Awesomium 1.7.3 SDK](http://www.awesomium.com/download) (includes the **Awesomium.NET** binaries and samples)
* [Awesomium.NET 1.7.3 API Reference](http://docs.awesomium.net)
* [Setting up on Windows](http://wiki.awesomium.net/getting-started/setting-up-on-windows.html)


### New Features

* Improved JavaScript Interoperation Framework (JIF) integration.
* (Unity) Full Mac OSX support (Mac OSX package).
* (Unity) Added the ability to create WebUIComponents that do not render their Surface. (see `WebUITarget.None`)
* (Unity) Improved design-time (Editor) support.
* (Unity) Added helper features that fix issues with creating a fullscreen player (new tab in Editor's Preferences).
* (Unity) Support for creating Mac OSX player from Windows.
* (WPF) Added design-time support for Visual Studio 2012 Update 3.
* (WPF) Improved UrlConverter to convert to and from string.
* (Awesomium.NET Source-Code Licensees) Major improvements in building automation.


### API Changes


#### New API:

##### *Awesomium.Core*

* [`WebConfig.UserScript`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebConfig_UserScript)

##### *Awesomium.Unity*

* [`WebUITarget.None`](http://docs.awesomium.net/unity/?tc=T_Awesomium_Unity_WebUITarget)
* [`WebUIComponent.Focus`](http://docs.awesomium.net/unity/?tc=M_Awesomium_Unity_WebUIComponent_Focus)
* [`WebUIComponent.Unfocus`](http://docs.awesomium.net/unity/?tc=M_Awesomium_Unity_WebUIComponent_Unfocus)

##### *Awesomium.Windows.Controls* (WPF)

* [`WebControlCommands.CopyImageURL`](http://docs.awesomium.net/?tc=P_Awesomium_Windows_Controls_WebControlCommands_CopyImageURL)


#### Modified API:

##### *Awesomium.Unity*

* `IResourceInterceptor` and `INavigationInterceptor` service, are **removed** from Unity (required features not supported by Mono).


### Bug Fixes

* Fixed bug within WebKit that caused GDI handles to constantly increase in main process. (Fix in native Awesomium)
* Fixed issues that would cause JavaScript dialogs in in the global scope or inline scripts, not be intercepted.
* Fixed a `NotSupportedException` that could be thrown when using a WPF or WinForms `WebSessionProvider` with child view.
* Fixed issue when handling multiple and simultaneous JavaScript `window.open` calls.

##### JavaScript (JIF)

* Fixed remaining "awejif is not defined" that occured in several occassions.

##### Unity

* Fixed focusing issues that would cause *"Bring In Front"* in GUI `WebUIComponent` instances to fail.
* Fixed issue that would cause the plugin and all components stop working when changing scenes.

##### MonoMac

* Fixed Awesomium framework bundle embedding issues with distributed MonoMac samples.


### Changes in Samples

* Minor improvements to *BasicSample*.
* (Unity) Improved the behavior of the address-box in the Unity *AwesomiumSample* scene.
* (WPF) Fixed issue in the *TabbedWPFSample* that would cause links in child windows (`PopupWindow`) not work appropriately.


### Known Issues

* On **Windows 8**, **WebGL** is currently not supported.


#### Under production:

* When you are using the OSX `OSMWebView`, drop-down (popup) menus (e.g., HTML: `<select>`), are not displayed automatically. Predefined drop-down (popup) menus have been added to the WPF `WebControl` and the Windows Forms `WebControl` but not to the MonoMac `OSMWebView` yet. However, the new powerful API allows you to design and display these yourself, by handling the [`ShowPopupMenu`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowPopupMenu) event.


### Older Changelogs

You can find this and previous Changelogs, under the [Changelogs](http://wiki.awesomium.net/changelogs/) category.
