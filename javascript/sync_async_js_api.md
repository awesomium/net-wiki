---
layout: page
title : Synchronous &amp; Asynchronous JavaScript API
group: JavaScript Integration
weight: 2

---

## Synchronous & Asynchronous API

Method calls to and from the web-page, are sent via a piped message to and from the child process. Such calls can be either *synchronous* or *asynchronous*.

Most managed handlers of [JavaScript-related events](#predefined_bindings) and of custom JavaScript methods (see [Declaring Custom Method Callbacks](#declaring_custom_method_callbacks)), are called in a **[Javascript Execution Context (JEC)](jec.html)**. Depending on how the handler is called, synchronously or asynchronously, a *synchronous* or *asynchronous* JEC is created.

**Read [this article](jec.html) for details about Javascript Execution Contexts**. 

Benefits and limitations of both contexts, are presented below:

### Synchronous Calls

Method calls that return a value are called synchronously which means that the child process is blocked until a value is returned to the initial caller, either this is the hosting application or client-side JavaScript. 

<a name="pros_sync"></a>
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

Likewise, JavaScript can synchronously call back to the hosting application through members of the [Javascript Interoperation Framework](jif.html) or through [custom JavaScript methods](#declaring_custom_method_callbacks):

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
{% highlight js %}
var userDiv = document.getElementById('userDiv');

if (userDiv) {
    // Invoke the custom 'getNewContent' method.
    userDiv.innerHTML = userDiv.getNewContent(userDiv.innerHTML);
}
{% endhighlight %}

<a name="cons_sync"></a>
#### Limitations

The major limitation of synchronous calls is that **you cannot make synchronous invocations from inside a synchronous JavaScript method/event handler, as this would cause a deadlock**:

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
    String readyState = webView.ExecuteJavascriptWithResult( "document.readyState" );
    // Throws an InvalidOperationException! You cannot make synchronous invocations 
    // from inside a synchronous JavaScript event handler.
}
{% endhighlight %}
{% highlight vbnet %}
AddHandler webView.JavascriptRequest, AddressOf webView_JavascriptRequest

[...]

' JavascriptRequest is called synchronously in response to OSMJIF.minimize, 
' OSMJIF.maximize, OSMJIF.restore and OSMJIF.exit.
Private Sub webView_JavascriptRequest(sender As Object, e As JavascriptRequestEventArgs)
    Dim readyState As String = webView.ExecuteJavascriptWithResult("document.readyState")
    ' Throws an InvalidOperationException! You cannot make synchronous invocations 
    ' from inside a synchronous JavaScript event handler.
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

### Asynchronous Calls

Asynchronous method calls are executed in the next update pass of the `WebCore`; they do not return a value and do not block the main or child process.

Handlers of asynchronous [custom JavaScript methods](#declaring_custom_method_callbacks) return no value (return `void` in C#) while JavaScript code in the web-page that invokes asynchronous methods will always receive *`undefined`* and code execution will resume immediately.

<a name="pros_async"></a>
#### Benefits

Methods that are called asynchronously do not have any of the [limitations of synchronous method calls](#cons_sync). What's more, since asynchronous methods are called in an *asynchronous* [Javascript Execution Context](jec.html), user code can access and use essential objects of the loaded web-page's current JavaScript environment through [`Global`](#global_class).

Here's a list of the major benefits of asynchronous method calls:

* **Performance**. Asynchronous method calls do not block the main or child process. Code execution resumes immediately after invoking an asynchronous method.
* **You can make synchronous calls from inside an asynchronous JavaScript method/event handler**.
* **You can pass JavaScript objects to an asynchronous JavaScript method handler**.
* **You can acquire and work with JavaScript objects from inside an asynchronous JavaScript method/event handler**. The easiest way to do this is by using the available instance of [`Global`](#global_class).

{% highlight csharp %}
// Create an asynchronous custom method and bind to it.
userDiv.BindAsync( "setNewContent", OnSetNewContent );

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
{% highlight js %}
var userDiv = document.getElementById('userDiv');

if (userDiv) {
    // Invoke the custom 'setNewContent' method.
    userDiv.setNewContent(userDiv);
}
{% endhighlight %}

<a name="cons_async"></a>
#### Limitations


