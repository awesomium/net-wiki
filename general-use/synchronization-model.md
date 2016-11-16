---
layout: page
title : Synchronization Model
group: General Use
weight: 7

---

### Overview

As it's known, Awesomium is not thread-safe. All calls to the Awesomium API should originate from the thread that initialized the `WebCore`, or created the first view or session, if the `WebCore` is not explicitly initialized.

Also, native Awesomium by design requires that users take care of updating the `WebCore` (by calling `WebCore.Update` at regular intervals), necessary for the core to conduct various operations such as updating the surface of offscreen views, maintaining session data, destroying any views that are queued for destruction and invoking any queued events.

In **Awesomium.NET** only, when a synchronization context was available because of the presence of a message loop on the thread Awesomium was running (UI threads), we took advantage of it by initiating *auto-update*, using system timers and then using the available synchronization context to invoke updates ourselves. This way, users did not need to worry about updating the `WebCore`, when using technology-specific views (WPF and WinForms `WebControl`, Cocoa `OSMWebView` and Unity `WebUIComponent`).

Certain limitations still remained however:

* Manual updating was still necessary when Awesomium was started on a non-UI thread, such as in a Console application or a Service.
* Users could not access Awesomium API from another thread, either this was a background thread or the UI thread if Awesomium was started on its own thread.
* Using the combination of asynchronous system timers and existing synchronization contexts to invoke updates on UI threads, had performance implications. System timers can fire from any thread-pool thread. Updates were posted, not sent and while we were updating, we would, if needed, discard certain *ticks* leading to an inconsistent *frame rate*.

Starting with **Awesomium.NET v1.7.4**, we have designed and applied a new, awesomium-specific synchronization model and we have utilized available .NET tools to the maximum, allowing *auto-update* in any scenario and cross-thread communucation with Awesomium.

### Awesomium Synchronization Context

If you are not using a technology-specific `WebControl`, or if you are developing an application without UI, you can now create a dedicated thread for Awesomium, start the `WebCore` from this thread and then interact with it cross-thread.

In a non-UI environment where no valid `SynchronizationContext` or synchronization model is available by .NET/Mono, Awesomium will create and install its own synchronization context and start an update loop on the thread, that will block the thread until the `WebCore` is shut down (see: [`WebCore.Shutdown`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Shutdown)).

> This means that even on non-UI threads, Awesomium.NET now provides *auto-update* and users should no longer manually call `Update`, a method already marked as obsolete.


**For more details, read the documentation of [`WebCore.Run`](http://docs.awesomium.net/?tc=M_Awesomium_Core_WebCore_Run)**.

The following samples available with the Awesomium and Awesomium .NET SDK, demonstrate starting the WebCore in a non-UI thread and interacting with it: cross-thread:

* **_BasicSample_**

  Simple sample that demonstrates using Awesomium in a Console application and interacting with it by monitoring events.

* **_BasicAsyncSample_**

 Like the above, only it starts Awesomium on a dedicated thread to allow the application keep running in its own thread accepting user input. It then interacts with Awesomium using tools of the new synchronization model.

