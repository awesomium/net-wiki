---
layout: page
title : Synchronous &amp; Asynchronous JavaScript API
group: JavaScript Integration
weight: 2

---

[external]: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAVklEQVR4Xn3PgQkAMQhDUXfqTu7kTtkpd5RA8AInfArtQ2iRXFWT2QedAfttj2FsPIOE1eCOlEuoWWjgzYaB/IkeGOrxXhqB+uA9Bfcm0lAZuh+YIeAD+cAqSz4kCMUAAAAASUVORK5CYII=

<p class="intro">This article provides details about <i>synchronous</i> and <i>asynchronous</i> communication between JavaScript and the hosting managed application.</p>

> Before you go through this article, please read an **[Introduction to JavaScript Integration in Awesomium](introduction.html)**.

### Introduction

Awesomium uses a multi-process architecture. Each [`IWebView`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IWebView) instance is isolated and rendered in a separate process. V8 and client-side JavaScript, also execute in these separate (child) processes. Method calls to and from the web-page, are sent via a piped message to and from the child process. Such calls can be either *synchronous* or *asynchronous*.

Most managed handlers of [JavaScript-related events](introduction.html#predefined-bindings) and the handlers of custom JavaScript methods (see [Declaring Custom Method Callbacks](introduction.html#declaring-custom-method-callbacks)), are called in a **[Javascript Execution Context (JEC)](jec.html)**. Depending on how the handler is called, synchronously or asynchronously, a *synchronous* or *asynchronous* JEC is created.

**Read [this article](jec.html) for details about Javascript Execution Contexts**. 

Benefits and limitations of both contexts, are presented below:

### Synchronous Calls

Method calls that return a value are called synchronously which means that the child process is blocked until a value is returned to the initial caller, either this is the hosting application or client-side JavaScript. 

#### Benefits

The **benefit** of synchronous calls, is the ability to synchronously return a response to the caller:

{% highlight csharp %}
// Synchronously invoke 'getElementById' allowing to get response.
JSObject userDiv = document.Invoke( "getElementById", "userDiv" );
{% endhighlight %}
{% highlight vbnet %}
' Synchronously invoke 'getElementById' allowing to get response.
Dim userDiv As JSObject = document.Invoke("getElementById", "userDiv")
{% endhighlight %}

Likewise, JavaScript can synchronously call back to the hosting application through members of the [Javascript Interoperation Framework](jif.html) or through [custom JavaScript methods](introduction.html#declaring-custom-method-callbacks):

{% highlight csharp %}
// Create a synchronous custom method and bind to it.
userDiv.Bind( "getNewContent", OnGetNewContent );

// Synchronous JSFunctionHandler that returns a value.
private JSValue OnGetNewContent( JSValue[] arguments )
{
    if ( arguments.Length < 1 )
        return String.Empty;
    if ( !arguments[ 0 ].IsString )
        return String.Empty;

    String divInnerHtml = arguments[ 0 ];

    return divInnerHtml.StartsWith( "Logged in as" ) ?
        divInnerHtml + @"<br/>" + System.Environment.UserName :
        String.Empty;
}
{% endhighlight %}
{% highlight vbnet %}
' Create a synchronous custom method and bind to it.
userDiv.Bind("getNewContent", AddressOf OnGetNewContent)

' Synchronous JSFunctionHandler that returns a value.
Private Function OnGetNewContent(arguments() As JSValue) As JSValue
    If arguments.Length < 1 Then Return String.Empty
    If Not arguments(0).IsString Then Return String.Empty

    Dim divInnerHtml As String = arguments(0)

    return If(divInnerHtml.StartsWith( "Logged in as" ), _
        divInnerHtml & "<br/>" & System.Environment.UserName, _
        String.Empty)
End Function
{% endhighlight %}

Client-side **JavaScript** code can now use our cutom method like so:

{% highlight js %}
var userDiv = document.getElementById('userDiv');

if (userDiv) {
    // Invoke the custom 'getNewContent' method.
    userDiv.innerHTML = userDiv.getNewContent(userDiv.innerHTML);
}
{% endhighlight %}

#### Limitations

The major limitation of synchronous calls is that **you cannot make synchronous invocations from inside a synchronous JavaScript method handler or JavaScript-related event, as this would cause a deadlock**:

{% highlight js %}
// Ask the hosting application to minimze the hosting window.
// Pass a boolean telling the application if the request is
// sent from the top window or a frame. 
var ret = OSMJIF.minimize(window === top);

if (ret) {
    console.log('Hosting window minimized');
}
{% endhighlight %}
{% highlight csharp %}
webView.JavascriptRequest += OnJavascriptRequest;

[...]

// JavascriptRequest is called synchronously in response to OSMJIF.minimize, 
// OSMJIF.maximize, OSMJIF.restore and OSMJIF.exit.
private void OnJavascriptRequest( Object sender, JavascriptRequestEventArgs e )
{
    String readyState = webView.ExecuteJavascriptWithResult(
        "document.readyState" );
    // Throws an InvalidOperationException! You cannot make synchronous
    // invocations from inside a synchronous JavaScript event handler.
}
{% endhighlight %}
{% highlight vbnet %}
AddHandler webView.JavascriptRequest, AddressOf webView_JavascriptRequest

[...]

' JavascriptRequest is called synchronously in response to OSMJIF.minimize, 
' OSMJIF.maximize, OSMJIF.restore and OSMJIF.exit.
Private Sub webView_JavascriptRequest(sender As Object, 
                                      e As JavascriptRequestEventArgs)
    Dim readyState As String = webView.ExecuteJavascriptWithResult(
        "document.readyState")
    ' Throws an InvalidOperationException! You cannot make synchronous
    ' invocations from inside a synchronous JavaScript event handler.
End Sub
{% endhighlight %}

> See a working example of handling the [`JavascriptRequest`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_JavascriptRequest) event, in the WPF **_WebControlSample_** project available with the [Awesomium SDK](../getting-started/setting-up-on-windows.html).

Due to the same limitation, **no JavaScript objects can be passed to a JavaScript method/event handler that is called synchronously**. This is because attempts to call synchronous methods of a `JSObject` or `JSFunction` from inside a synchronous handler, would also cause a deadlock. Any such objects passed from JavaScript to the hosting application, are replaced with [`JSValue.Undefined`](http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Undefined) in the arguments array passed to the managed handler:

{% highlight csharp %}
// Create a synchronous custom method and bind to it.
userDiv.Bind( "setNewContent", OnSetNewContent );

// Synchronous JSFunctionHandler that returns a value.
private JSValue OnSetNewContent( JSValue[] arguments )
{
    if ( arguments.Length < 1 )
        return String.Empty;
    
    JSObject userDiv = arguments[ 0 ];

    // Will be an invalid JSObject that is converted to false 
    // because the argument is JSValue.Undefined.
    if ( !userDiv )
        return;

    [...]
}
{% endhighlight %}
{% highlight vbnet %}
' Create a synchronous custom method and bind to it.
userDiv.Bind("setNewContent", AddressOf OnSetNewContent)

' Synchronous JSFunctionHandler that returns a value.
Private Function OnSetNewContent(arguments() As JSValue) As JSValue
    If arguments.Length < 1 Then Return String.Empty
    
    Dim userDiv As JSObject = arguments(0)

    ' Will be an invalid JSObject that is converted to false 
    ' because the argument is JSValue.Undefined.
    If Not CBool(userDiv) Then Return String.Empty

    [...]
End Function
{% endhighlight %}
{% highlight js %}
var userDiv = document.getElementById('userDiv');

if (userDiv) {
    // Invoke the custom 'setNewContent' method attempting
    // to pass the object itself.
    userDiv.setNewContent(userDiv);
}
{% endhighlight %}

Finally, multiple **synchronous calls can affect the performance of your application**. Consider the following example (using DLR):

{% highlight csharp %}
private void OnDocumentReady( object sender, DocumentReadyEventArgs e )
{
    // When ReadyState is Ready, you can execute JavaScript against
    // the DOM but all resources are not yet loaded. Wait for Loaded.
    if ( e.ReadyState == DocumentReadyState.Ready )
        return;

    // Get the current Global environment.
    var global = e.Environment;

    if ( !global )
        return;

    // Synchronous call here for |getElementsByTagName|.
    var images = global.document.getElementsByTagName( "img" );

    // Another synchronous call here for |images.length|.
    // This is because |images| is not a managed Array of JSValue,
    // but a JSObject representing a JavaScript |NodeList| object.
    // This is why we do not use foreach...in since |NodeList| is not
    // enumerable. As per JSObject specifications (similarly to
    // JavaScript), a foreach...in loop would iterate the names of
    // the NodeList's enumerable properties, such as "0", "1", "2" etc.,
    // including "length", since |length| is just a property of the object.
    for ( int i = 0; i < images.length; i++ )
    {
        // Another synchronous call here. As mentioned above |images|
        // is not an Array but a |NodeList| object. The indexer here,
        // simply acquires the value of a named data property, named
        // after the index. For example, images[0] and images["0"]
        // would both return the same item.
        var img = images[ i ];
        // Another synchronous call here to acquire the value of
        // the |src| property.
        string src = img.src;

        if ( src.EndsWith( ".png" ) )
        {
            // Finally, another synchronous call here to set a
            // new value.
            img.src = src.Replace( ".png", ".jpg" );
        }
    }
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

Private Sub OnDocumentReady(sender As Object, e As DocumentReadyEventArgs)
    ' When ReadyState is Ready, you can execute JavaScript against
    ' the DOM but all resources are not yet loaded. Wait for Loaded.
    If e.ReadyState = DocumentReadyState.Ready Then Return

    ' Get the current Global environment.
    Dim js As Global = e.Environment

    If Not CBool(js) Then Return

    ' Synchronous call here for |getElementsByTagName|.
    Dim images = global.document.getElementsByTagName("img")

    ' Another synchronous call here for |images.length|.
    ' This is because |images| is not a managed Array of JSValue,
    ' but a JSObject representing a JavaScript |NodeList| object.
    ' This is why we do not use foreach...in since |NodeList| is not
    ' enumerable. As per JSObject specifications (similarly to
    ' JavaScript), a foreach...in loop would iterate the names of
    ' the NodeList's enumerable properties, such as "0", "1", "2" etc.,
    ' including "length", since |length| is just a property of the object.
    For i As Integer = 0 To images.length - 1
        ' Another synchronous call here. As mentioned above |images|
        ' is not an Array but a |NodeList| object. The indexer here,
        ' simply acquires the value of a named data property, named
        ' after the index. For example, images[0] and images["0"]
        ' would both return the same item.
        Dim img = images(i)
        ' Another synchronous call here to acquire the value of
        ' the |src| property.
        String src = img.src

        If src.EndsWith(".png") Then
            ' Finally, another synchronous call here to set a
            ' new value.
            img.src = src.Replace(".png", ".jpg");
        End If
    Next
End Sub
{% endhighlight %}

If there are hundreds of images in your page, say 200, the code above would cause 602 synchronous IPC messages sent to the child process, each one blocking the hosting and child processes until a response is provided.

This should be rewritten to make sure that:

* Only code that needs information provided by the host, should be executed on the managed side.
* Code takes advantage of asynchronous calls and callback model as much as possible.

Here is an example:

{% highlight csharp %}
webView.JavascriptMessage += OnJavascriptMessage;

[...]

private void OnDocumentReady( object sender, DocumentReadyEventArgs e )
{
    // When ReadyState is Ready, you can execute JavaScript against
    // the DOM but all resources are not yet loaded. Wait for Loaded.
    if ( e.ReadyState == DocumentReadyState.Ready )
        return;

    // Called asynchronously and injects JavaScript in the page
    // that does most of the job.
    webView.ExecuteJavascript( 
        "function processImages(sources) { " + 
            "var images = document.getElementsByTagName('img'); " +
            "for (var i = 0; i < images.length; i++) { " +
                "console.log(images[i].src + ' -> ' + sources[i]); " + 
                "images[i].src = sources[i]; " + 
            "} " + 
        "}; " + 
        "(function() { " +
            "var images = document.getElementsByTagName('img'); " + 
            "var sources = []; " +
            "for (var i = 0; i < images.length; i++) { " +
                "sources.push(images[i].src); " + 
            "} " + 
            "OSMJIF.sendMessageAsync('images', processImages, sources); " + 
        "})();" );
}

// Called asynchronously in response to |OSMJIF.sendMessageAsync|.
// The result provided is passed to the |processImages| callback
// that is also called asynchronously. 
void OnJavascriptMessage( object sender, JavascriptMessageEventArgs e )
{
    if ( e.Message != "images" || !e.Arguments[ 0 ].IsArray )
        return;

    JSValue[] sources = (JSValue[])e.Arguments[ 0 ];

    for ( int i = 0; i < sources.Length; i++ )
    {
        String src = sources[ i ];
        if ( src.EndsWith( ".png" ) )
        {
            sources[ i ] = src.Replace( ".png", ".jpg" );
        }
    }

    e.Result = sources;
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

Private Sub OnDocumentReady(sender As Object, e As DocumentReadyEventArgs)
    ' When ReadyState is Ready, you can execute JavaScript against
    ' the DOM but all resources are not yet loaded. Wait for Loaded.
    If e.ReadyState = DocumentReadyState.Ready Then Return

    ' Called asynchronously and injects JavaScript in the page
    ' that does most of the job.
    webView.ExecuteJavascript(
        "function processImages(sources) { " &
            "var images = document.getElementsByTagName('img'); " &
            "for (var i = 0; i < images.length; i++) { " &
                "console.log(images[i].src + ' -> ' + sources[i]); " &
                "images[i].src = sources[i]; " &
            "} " &
        "}; " &
        "(function() { " &
            "var images = document.getElementsByTagName('img'); " &
            "var sources = []; " +
            "for (var i = 0; i < images.length; i++) { " &
                "sources.push(images[i].src); " &
            "} " &
            "OSMJIF.sendMessageAsync('images', processImages, sources); " &
        "})();")
End Sub

' Called asynchronously in response to |OSMJIF.sendMessageAsync|.
' The result provided is passed to the |processImages| callback
' that is also called asynchronously. 
Private Sub OnJavascriptMessage(sender As Object, e As JavascriptMessageEventArgs)
    Handles webView.JavascriptMessage

    If (e.Message <> "images") OrElse Not e.Arguments(0).IsArray Then Return

    Dim sources As JSValue() = CType(e.Arguments(0), JSValue())

    For i As Integer = 0 To sources.Length - 1
        Dim src As String = sources(i)
        If src.EndsWith(".png") Then
            sources(i) = src.Replace(".png", ".jpg")
        End If
    Next

    e.Result = sources
End Sub
{% endhighlight %}

### Asynchronous Calls

Asynchronous method calls are executed in the next update pass of the `WebCore`; they do not return a value and do not block the main or child process.

Handlers of asynchronous [custom JavaScript methods](introduction.html#declaring-custom-method-callbacks) return no value (return `void` in C#) while JavaScript code in the web-page that invokes asynchronous methods will always receive *`undefined`* and code execution will resume immediately.

#### Benefits

Methods that are called asynchronously do not have any of the [limitations of synchronous method calls](#limitations). What's more, since asynchronous methods are called in an *asynchronous* [Javascript Execution Context](jec.html), user code can access and use essential objects of the loaded web-page's current JavaScript environment through [`Global`](introduction.html#global-class).

Here's a list of the major benefits of asynchronous method calls:

* **Performance**. Asynchronous method calls do not block the main or child process. Code execution resumes immediately after invoking an asynchronous method.
* **You can make synchronous calls from inside an asynchronous JavaScript method/event handler**.
* **You can pass JavaScript objects to an asynchronous JavaScript method handler**.
* **You can acquire and work with JavaScript objects from inside an asynchronous JavaScript method/event handler**. The easiest way to do this is by using the available instance of [`Global`](introduction.html#global-class).
* **An instance of [`Global`](introduction.html#global_class) is available within every asynchronous [Javascript Execution Context (JEC)](jec.html)**. It can either be accessed through the `Environment` property of the `EventArgs` of a JavaScript-related event (e.g. [`DocumentReadyEventArgs.Environment`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_Environment)), or through [`Global.Current`](http://docs.awesomium.net/?tc=P_Awesomium_Core_Global_Current).

{% highlight csharp %}
// Create an asynchronous custom method and bind to it.
userDiv.BindAsync( "setNewContent", OnSetNewContent );

[...]

// Asynchronous JSFunctionAsyncHandler.
private void OnSetNewContent( JSValue[] arguments )
{
    if ( arguments.Length < 1 )
        return;

    // You can access 'Global' from inside an asynchronous 
    // method handler, executed in an asynchronous JEC.
    var global = Global.Current;

    if ( !global )
        return;

    // Note that acquiring the value of a property involves
    // a synchronous call to the remote object, but we can
    // do this from inside an asynchronous method handler.
    var readyState = global.document.readyState;

    // 'readyState' is a JSValue but can be implicitly
    // converted to String.
    if ( readyState != "complete" )
        return;

    // You can pass objects to asynchronous method handlers.    
    dynamic userDiv = (JSObject)arguments[ 0 ];

    // Will be true.
    if ( !userDiv )
        return;

    // Another synchronous call.
    String divInnerHtml = userDiv.innerHTML;

    // Setting the property dynamically, is also performed synchronously.
    userDiv.innerHTML = divInnerHtml.StartsWith( "Logged in as" ) ?
        divInnerHtml + @"<br/>" + System.Environment.UserName :
        String.Empty;
}
{% endhighlight %}
{% highlight vbnet %}
' Create an asynchronous custom method and bind to it.
userDiv.BindAsync("setNewContent", AddressOf OnSetNewContent)

[...]

' Asynchronous JSFunctionAsyncHandler.
Private Sub OnSetNewContent(arguments() As JSValue)
    If arguments.Length < 1 Then Return
    
    ' You can access 'Global' from inside an asynchronous 
    ' method handler, executed in an asynchronous JEC.
    Dim js As [Global] = [Global].Current

    If Not CBool(js) Then Return

    ' Note that acquiring the value of a property involves
    ' a synchronous call to the remote object, but we can
    ' do this from inside an asynchronous method handler.
    Dim readyState As Object = js.document.readyState

    ' 'readyState' is a JSValue but can be implicitly
    ' converted to String.
    If readyState <> "complete" Then Return

    ' You can pass objects to asynchronous method handlers.    
    Dim userDiv As JSObject = CType(arguments(0), JSObject)

    ' Will be True.
    If Not CBool(userDiv) Then Return

    ' Another synchronous call.
    Dim divInnerHtml As String = userDiv.innerHTML

    ' Setting the property dynamically, is also performed synchronously.
    userDiv.innerHTML = If(divInnerHtml.StartsWith("Logged in as"), _
        divInnerHtml & "<br/>" & System.Environment.UserName, _
        String.Empty)
End Sub
{% endhighlight %}

Client-side **JavaScript**:

{% highlight js %}
var userDiv = document.getElementById('userDiv');

if (userDiv) {
    // Invoke the custom 'setNewContent' method.
    userDiv.setNewContent(userDiv);
}
{% endhighlight %}

#### Limitations

Any limitations with asynchronous calls, originate from the very fact that the calls are made asynchronously. If you combine synchronous and asynchronous calls, your code may have to be redesigned to either use a **_callback model_** or force a synchronous call whenever necessary.

The following examples demonstrate issues that you may come up with when combining synchronous and synchronous calls, and how to deal with them:

 