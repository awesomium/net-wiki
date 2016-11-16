---
layout: page
title : Introduction to JavaScript Integration
group: JavaScript Integration
weight: 1

---

[external]: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAVklEQVR4Xn3PgQkAMQhDUXfqTu7kTtkpd5RA8AInfArtQ2iRXFWT2QedAfttj2FsPIOE1eCOlEuoWWjgzYaB/IkeGOrxXhqB+uA9Bfcm0lAZuh+YIeAD+cAqSz4kCMUAAAAASUVORK5CYII=

<p class="intro">It's easy to communicate with web-pages using JavaScript; here's an introduction to the JavaScript Integration features provided by Awesomium and Awesomium.NET.</p>

### Index

* [The V8 JavaScript Engine](#the_v8_javascript_engine)
* [Awesomium JavaScript Integration API](#awesomium_javascript_integration_api)
  * [Predefined Bindings](#predefined_bindings)
  * [JavaScript Evaluation](#javascript_evaluation)
  * [JSValue Class](#jsvalue_class)
      * [Conversion Details](#conversion_details)
      * [String](#string)
      * [Boolean](#boolean)
      * [Array](#array)
      * [Object](#object)
      * [Function](#function)
      * [Null & Undefined](#null__undefined)
  * [JSObject Class](#jsobject_class)
      * [Local JSObjects](#local_jsobjects)
      * [Remote JSObjects](#remote_jsobjects)
      * [Calling JavaScript Functions](#calling_javascript_functions)
      * [Lifetime of Objects](#lifetime_of_objects)
      * [Dynamic Language Runtime](#dynamic_language_runtime)
  * [Global Class](#global_class)
* [Declaring Custom Method Callbacks](#declaring_custom_method_callbacks)
  * [Examples](#examples)
      * [CLR Example](#clr_example)
      * [DLR Example](#dlr_example)
* [Global JavaScript Objects](#global_javascript_objects)    
* [Synchronous & Asynchronous API](#synchronous__asynchronous_api)
* [Handling Errors](#handling_errors)
  * [Native Errors](#native_errors)
  * [JavaScript Errors](#javascript_errors)
  * [Binding Errors](#binding_errors)
  * [Javascript Execution Context](#javascript_execution_context)
* [Additional Resources](#additional_resources)


## The V8 JavaScript Engine

Awesomium incorporates the V8 JavaScript Engine that implements ECMAScript as specified in ECMA-262, 5th edition.

V8 compiles and executes JavaScript source code, handles memory allocation for objects, and garbage collects objects it no longer needs.

JavaScript is used for client-side scripting in Awesomium and it's being used to manipulate Document Object Model (DOM) objects for example. V8 provides all the data types, operators, objects and functions specified in the ECMA standard while Awesomium provides the standard DOM and the API that bridges the two worlds, that of the hosting application and that of the web-page.

In addition, Awesomium.NET extends the available native Awesomium JavaScript bridge adding features that make interaction of managed code with web-pages and vice versa, much easier.

## Awesomium JavaScript Integration API

Interaction between a managed hosting application and a web-page using JavaScript, can be initiated by either the hosting application using dedicated methods of [`IWebView`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IWebView) and [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject) or by client-side code in a web-page using API of the [JavaScript Interoperation Framework (JIF)](jif.html) or by invoking [custom JavaScript methods](#declaring_custom_method_callbacks).

Awesomium uses a multi-process architecture. Each [`IWebView`](http://docs.awesomium.net/?tc=T_Awesomium_Core_IWebView) instance is isolated and rendered in a separate process. V8 and client-side JavaScript, also execute in these separate (child) processes. Method calls to and from the web-page, are sent via a piped message. Such calls can be either *synchronous* or *asynchronous*. **For details, read the [Synchronous & Asynchronous API](#synchronous__asynchronous_api) section below**.

### Predefined Bindings

Awesomium.NET provides predefined bindings to several standard DOM `window` and `document` methods or events. The following list presents `IWebView` events fired when certain `window` methods or `document` events are called:

* [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady)

    Fired **asynchronously** in response to a `document` **_[`readystatechange`](https://developer.mozilla.org/en-US/docs/Web/Events/readystatechange)_** ![][external] event. This is usually the place where you can start interacting with the DOM. The event handler is called in a **[Javascript Execution Context (JEC)](jec.html)**. 

    For details, read the following articles or sections:

    * [Javascript Execution Context (JEC)](jec.html)
    * [Initialization Sequence](../general-use/initialization-sequence.html)
    * [Synchronous & Asynchronous API](#synchronous__asynchronous_api)
* [`ShowJavascriptDialog`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowJavascriptDialog)

    Fired **synchronously** in response to **_`window.alert`_**, **_`window.confirm`_** and **_`window.prompt`_**. Technology specific WebControls show predefined dialogs for these calls but you can handle the event and either cancel it or modally display your own dialog or silently provide a response, where one is expected.

    > The *windowless* [`WebView`](http://docs.awesomium.net/?tc=T_Awesomium_Core_WebView) component provides no predefined UI so you should handle the event if you want to show your dialogs. Otherwise the event is silently canceled.
* [`ShowCreatedWebView`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView)

    Fired **asynchronously** when an `IWebView` instance creates a new child view as the result of clicking on a link with `target="_blank"` or submitting a form with `target="_blank"` but also as the result of a JavaScript **_`window.open`_** call.

    For details, read the documentation of the [`ShowCreatedWebView`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ShowCreatedWebView) event.
* [`PrintRequest`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_PrintRequest)

    Occurs when the page requests to print itself, usually the result of **_`window.print`_** being called from JavaScript. It is your responsibility to print the view to a file (usually using [`PrintToFile`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_PrintToFile)) and handle the actual device printing.
* [`WindowClose`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_WindowClose)

    Fired **asynchronously** when JavaScript in the page calls **_`window.close`_**. You can ignore this event, or respond depending on the current context. For example, if the page is open in the main window of an application or a tab, browsers display a confirmation dialog asking users if they want to acknowledge the page's request. If the page is open in a popup window, browsers usually respect the request and close the popup window, without any confirmation from the user.
* [`ConsoleMessage`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ConsoleMessage)

    Occurs when a message is added to the console on the page. This is usually the result of a JavaScript error being encountered on a page or the result of an explicit **_`console.log`_** call.

> Predefined events are also provided for methods of the **Javascript Interoperation Framework (JIF)** called from JavaScript. The handlers of these events are also called in a **Javascript Execution Context (JEC)**. For details, read the following articles:
> 
> * [JavaScript Interoperation Framework (JIF)](jif.html)
> * [Javascript Execution Context (JEC)](jec.html)

### JavaScript Evaluation

You can evaluate and execute JavaScript code against the currently loaded web-page, using the following `IWebView` members:

* [`ExecuteJavascript`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_IWebView_ExecuteJavascript)

    Asynchronously executes JavaScript code against the currently loaded web-page.
* [`ExecuteJavascriptWithResult`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_IWebView_ExecuteJavascriptWithResult)

    Synchronously executes JavaScript code against the currently loaded web-page and returns a result, encapsulated by a [`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue).

> Think of these two methods as equivalent to JavaScript [`eval()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval) ![][external].

### JSValue Class

JavaScript values passed to the managed hosting application, are always encapsulated by a **[`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue)** instance. Similarly, values passed to the client-side from the application, are converted to a **`JSValue`** before they are sent to the web-page.

> Starting with **v1.7.5**, `JSValue` has been remodelled as a `class` (it is no longer a `struct`) although it remains immutable from the application's point of view. For details, read: [Important Changes in v1.7.5]()

The [`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue) class is used as an intermediary to translate JavaScript types into their managed equivalent and vice-versa. To get a better understanding of how this relationship works, here’s how types are mapped between JavaScript and .NET using `JSValue`:

<table>
<tr><th>JavaScript</th><th>.NET</th><th>Conversion</th></tr>

<tr><td><code>Boolean</code></td><td><a href="http://msdn2.microsoft.com/en-us/library/a28wyd50" target="_blank">Boolean</a></td><td>You can explicitly or implicitly cast from and to a <code>JSValue</code>.<p></p>Any <code>JSValue</code> can be converted to <code>Boolean</code>. For details, see the <b>Conversion Details</b> below.</td></tr>

<tr><td><code>Integer</code></td><td><a href="http://msdn2.microsoft.com/en-us/library/td2s409d" target="_blank">Int32</a></td><td>You can explicitly cast from and explicitly or implicitly cast to, a <code>JSValue</code>.</td></tr>

<tr><td><code>Double</code></td><td><a href="http://msdn2.microsoft.com/en-us/library/643eft0t" target="_blank">Double</a></td><td>You can explicitly cast from and explicitly or implicitly cast to, a <code>JSValue</code>.</td></tr>

<tr><td><code>String</code></td><td><a href="http://msdn2.microsoft.com/en-us/library/s1wwdcbf" target="_blank">String</a></td><td>You can explicitly or implicitly cast from and to a <code>JSValue</code>.<p></p>You can get the textual representation of any JavaScript value, even if it is not a <code>String</code>.</td></tr>

<tr><td><code>Array</code></td><td><code>JSValue[]</</code></td><td>You can explicitly cast from and explicitly or implicitly cast to, a <code>JSValue</code>.<p></p><code>JSValue</code> provides an indexer that allows you to acquire or edit the members of a JavaScript <code>Array</code> directly through <code>JSValue</code>.</td></tr>

<tr><td><code>Object</code></td><td><a href="http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject" target="">JSObject</a></td><td>You can explicitly or implicitly cast from and to a <code>JSValue</code>.<p></p>Any <code>JSValue</code> can be converted to <code>JSObject</code>. If the <code>JSValue</code> does not represent a JavaScript <code>Object</code>, a special invalid (undefined) <code>JSObject</code> is returned that when checked against <code>null</code> (<code>Nothing</code> in Visual Basic) will return <code>true</code> and when converted to <code>Boolean</code> will be converted to <code>false</code>.</td></tr>

<tr><td><code>Function</code></td><td><a href="http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction" target="">JSFunction</a></td><td>You can explicitly or implicitly cast from and to a <code>JSValue</code>.<p></p>Any <code>JSValue</code> can be converted to <code>JSFunction</code>. If the <code>JSValue</code> does not represent a JavaScript <code>Function</code>, a special invalid (undefined) <code>JSFunction</code> is returned that when checked against <code>null</code> (<code>Nothing</code> in Visual Basic) will return <code>true</code> and when converted to <code>Boolean</code> will be converted to <code>false</code>.</td></tr>

<tr><td><code>NaN</code></td><td><a href="http://msdn.microsoft.com/en-us/library/system.double.nan.aspx" target="_blank">NaN</a></td><td>You can explicitly cast from and explicitly or implicitly cast to, a <code>JSValue</code>.<p></p>A <code>JSValue</code> representing JavaScript <code>NaN</code>, is still a <code>Double</code> (<code>JSValue.IsDouble</code> returns <code>true</code>) but an additional <code>JSValue.IsNaN</code> property is available that will also return <code>true</code>.</td></tr>

<tr><td><code>Infinity</code></td><td><a href="http://msdn.microsoft.com/en-us/library/system.double.positiveinfinity.aspx" target="_blank">PositiveInfinity</a><br /><a href="http://msdn.microsoft.com/en-us/library/system.double.negativeinfinity.aspx" target="_blank">NegativeInfinity</a></td><td>You can explicitly cast from and explicitly or implicitly cast to, a <code>JSValue</code>.<p></p>A <code>JSValue</code> representing JavaScript <code>Infinity</code>, is still a <code>Double</code> (<code>JSValue.IsDouble</code> returns <code>true</code>) but an additional <code>JSValue.IsInfinity</code> property is available that will also return <code>true</code>.</td></tr>


<tr><td><code>Null</code></td><td><a href="http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Null" target="">Null</a></td><td>Any <code>null</code> reference passed to the client-side, will be converted to <a href="http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Null" target="">Null</a></td></tr>
<tr><td><code>Undefined</code></td><td><a href="http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Undefined" target="">Undefined</a></td><td>Any unsupported value passed to the client-side, will be converted to <a href="http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Undefined" target="">Undefined</a></td></tr>
</table>

#### Conversion Details

`JSValue` supports *explicit* and, for some types, *implicit* casting to and from the managed equivalent of all the supported data types. All *implicit* conversions are guaranteed to succeed on any type of `JSValue`. Conversions that require an *explicit* casting, will throw an `InvalidCastException` if the `JSValue` does not represent the equivalent JavaScript type. 

{% highlight csharp %}
String windowStr = webView.ExecuteJavascriptWithResult( "window" );
// The conversion succeeds and the value of windowStr is "[object DOMWindow]".

int windowInt = (int)webView.ExecuteJavascriptWithResult( "window" );
// Throws an InvalidCastException.
{% endhighlight %}
{% highlight vbnet %}
Dim windowStr As JSObject = webView.ExecuteJavascriptWithResult("window")
' The conversion succeeds and the value of windowStr is "[object DOMWindow]".

Dim windowInt As Integer = CType(webView.ExecuteJavascriptWithResult("window"), Int32)
' Throws an InvalidCastException.
{% endhighlight %}

You rarely need to work with the `JSValue` itself. However, it can be useful if you need to check the value type before casting.

{% highlight csharp %}
JSValue windowInt = webView.ExecuteJavascriptWithResult( "window" );

if ( !windowInt.IsNumber )
    return;
{% endhighlight %}
{% highlight vbnet %}
Dim windowInt As JSValue = webView.ExecuteJavascriptWithResult("window")

If Not windowInt.IsNumber Then
    Return
End If
{% endhighlight %}

#### String

You can get the textual representation of any `JSValue` via [`JSValue.ToString`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSValue_ToString). **It is safe to call this on a `JSValue` of any type.** This is equivalent to casting the `JSValue` to `String`. An *implicit* operator is available that will succeed on any type of `JSValue`. Even casting a managed null `JSValue` reference, will return `"null"`.

#### Boolean

Any `JSValue` can be implicitly casted to `Boolean`. The conversion depends on the type of the value encapsulated by the `JSValue`. The following list presents how a `JSValue` is converted to a managed `Boolean`, based on the JavaScript value it encapsulates:

* A JavaScript `Boolean` value is converted to its equivalent managed `Boolean` value.
* Any non-zero JavaScript number is converted to `True`. `0` is converted to `False`;
* A JavaScript `Array` is converted to `True`.
* A valid JavaScript `Object` or `Function` is converted to `True`; an invalid (undefined) one, is converted to `False`. `JSObject` and `JSFunction` themselves also implement an implicit operator to `Boolean` that behaves the same.
* JavaScript `NaN` is converted to `False`.
* JavaScript `Infinity` is converted to `True`.
* JavaScript `null` is converted to `False`. A managed null `JSValue` reference will also be converted to `False`.
* JavaScript `undefined` is converted to `False`.

#### Array

On the .NET side, JavaScript Arrays are modeled as a managed array of `JSValue`. A `JSValue` that represents a JavaScript `Array`, can be **explicitly** casted to a managed array of `JSValue` but `JSValue` also contains an indexer property that allows you to access and even edit members of the JavaScript array directly from the `JSValue` wrapper. 

So, for example, if you had the following array in Javascript:

{% highlight js %}
var myArray = [1, 2, "three", 4, "five"];
{% endhighlight %}
	
You could access it in managed code like so:

{% highlight csharp %}
JSValue myValue = webView.ExecuteJavascriptWithResult( "myArray" );

if( !myValue.IsArray )
    return;

JSArray[] myArray = (JSValue[])myValue;

int first = (int)myArray[ 0 ];
// Value of first is '1'.
int second = (int)myArray[ 1 ];
// Value of second is '2'.
String third = myArray[ 2 ];
// Value of third is "three".

// Or you can access and even edit the array, directly from 
// JSValue which is safer as demonstrated below:
first = (int)myValue[ 0 ];
// Value of first is '1'.
second = (int)myValue[ 1 ];
// Value of second is '2'.
third = myValue[ 2 ];
// Value of third is "three".
String forth = myValue[ 3 ];
// Value of forth is "undefined".
myValue[ 3 ] = "four";
forth = myValue[ 3 ];
// Value of forth is "four".
{% endhighlight %}

#### Object

As specified in the table above, JavaScript Objects are modeled in .NET with the [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject) class.

A `JSValue` that encapsulates a JavaScript `Object`, has [`JSValue.IsObject`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsObject) set to `true` and can be implicitly casted to `JSObject`. Converting a `JSValue` to `JSObject` succeeds even if the `JSValue` does not represent a JavaScript `Object`. In this case a special invalid (undefined) `JSObject` is returned ([`JSObject.Type`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSObject_Type) is [`JSObjectType.Invalid`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObjectType)) that when checked against `null` will return `true` and when converted to `Boolean` will be converted to `false`:

{% highlight csharp %}
// ExecuteJavascriptWithResult returns a JSValue which in this
// case it will represent the returned number but casting to
// JSObject will still succeed, returning an invalid JSObject.
JSObject myObject = webView.ExecuteJavascriptWithResult( "42" );

if ( !myObject )
    return;
{% endhighlight %}
{% highlight vbnet %}
' ExecuteJavascriptWithResult returns a JSValue which in this
' case it will represent the returned number but casting to
' JSObject will still succeed, returning an invalid JSObject.
Dim myObject As JSObject = webView.ExecuteJavascriptWithResult("42")

If Not CBool(myObject) Then Return
{% endhighlight %}

There are two types of JSObjects, **Local** and **Remote**.

**For details, see the [JSObject Class](#jsobject_class) section below**.

#### Function

JavaScript functions are **[Remote](#remote_jsobjects)** JSObjects that live within the V8 engine in the child process and are modeled in .NET with the [`JSFunction`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction) class. JavaScript `Function` objects that are passed to the hosting application, are automatically wrapped by a `JSFunction` (that is a `JSObject` subclass).

A `JSValue` that encapsulates a JavaScript `Function`, has both [`JSValue.IsObject`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsObject) and [`JSValue.IsFunctionObject`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsFunctionObject) set to `true` and can be implicitly casted to `JSFunction`. Converting a `JSValue` to `JSFunction` succeeds even if the `JSValue` does not represent a JavaScript `Function`. In this case a special invalid (undefined) `JSFunction` is returned ([`JSObject.Type`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSObject_Type) is [`JSObjectType.Invalid`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObjectType)) that when checked against `null` will return `true` and when converted to `Boolean` will be converted to `false`.

{% highlight csharp %}
JSObject element = webView.ExecuteJavascriptWithResult( "document.getElementById('link_a')" );

if ( !element )
    return;

// Create and get a JavaScript Function object.
JSFunction myFunction = webView.ExecuteJavascriptWithResult( 
    "new Function('return this instanceof HTMLAnchorElement;')" );

if ( !myFunction )
    return;

if ( myFunction.Call( element ) )
{
    // It is a link. Simulate a 'click'
    element.InvokeAsync( "click" );
}
{% endhighlight %}
{% highlight vbnet %}
Dim element As JSObject = webView.ExecuteJavascriptWithResult("document.getElementById('link_a')")

If Not CBool(element) Then Return

' Create and get a JavaScript Function object.
Dim myFunction As JSFunction = webView.ExecuteJavascriptWithResult( _
    "new Function('return this instanceof HTMLAnchorElement;')")

If Not CBool(myFunction) Then Return

If myFunction.Call( element ) Then
    ' It is a link. Simulate a 'click'
    element.InvokeAsync("click")
End If
{% endhighlight %}

`JSFunction` provides a set of constructors that allow you to create an instance of `JSFunction` that represents an anonymous JavaScript `Function` bound to a managed handler. Such `JSFunction` objects can be assigned as the value of a property of a  *remote* or even *local* object (thus creating a *custom method*) or be passed as arguments to a JavaScript method call.

**For details, read**: 

* [JSObject Class](#jsobject_class)
* [Creating Custom JavaScript Methods](custom-javascript-methods.html)

#### Null & Undefined

The global static [`JSValue.Null`](http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Null) and [`JSValue.Undefined`](http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Undefined) representing JavaScript `null` and `undefined` respectively, are always available to managed code and can be passed as arguments to JavaScript method calls, set as value of a `JSObject` property or returned from custom JavaScript method handlers.

Any managed null reference passed to JavaScript, is converted to `JSValue.Null` before sent to the web-page that will receive it as JavaScript `null`.

JavaScript methods that do not return a specific value and are called synchronously (using `JSObject.Invoke`) or JavaScript code that does not return a value but is executed synchronously (using `IWebView.ExecuteJavascriptWithResult`), return `JSValue.Undefined`.

Attempting to acquire the value of a JavaScript object's member that does not exist or in general, any operation that would return `undefined` in JavaScript, returns `JSValue.Undefined` in managed code.

**Both `JSValue.Null` and `JSValue.Undefined` are implicitly convertible to a managed `false` `Boolean` value**.

> Errors that may occur during the execution of a synchronous JavaScript call, are usually silently handled and either reported to the JavaScript console (see [`IWebView.ConsoleMessage`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ConsoleMessage)) or reported through [`JSObject.GetLastError`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_GetLastError) or [`IWebView.GetLastError`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_GetLastError). In these cases, the synchronous method calls that failed also return `JSValue.Undefined`. **For details, see the [Handling Errors](#handling_errors) section below**.

{% highlight csharp %}
JSValue someVar = webView.ExecuteJavascriptWithResult( "someVar" );
{% endhighlight %}
{% highlight vbnet %}
Dim someVar As JSValue = webView.ExecuteJavascriptWithResult("someVar")
{% endhighlight %}

You can now check if *`someVar`* is defined and represents a valid usable value, using any of the following two methods:

{% highlight csharp %}
if ( someVar.IsUndefined )
{
    // The call returned JSValue.Undefined but this does not
    // necessarily mean that 'someVar' is 'undefined'. The
    // synchronous call may have failed.
    if ( webView.GetLastError() == Error.None )
    {
        // 'someVar' is definitely undefined. 
    }
}
{% endhighlight %}
{% highlight vbnet %}
If someVar.IsUndefined Then
    ' The call returned JSValue.Undefined but this does not
    ' necessarily mean that 'someVar' is 'undefined'. The
    ' synchronous call may have failed.
    If webView.GetLastError() = Error.None Then
        ' 'someVar' is definitely undefined. 
    End If
End If
{% endhighlight %}

{% highlight csharp %}
if ( !someVar )
{
    // 'someVar' is not a valid usable value or object but this 
    // does not necessarily mean that 'someVar' is 'undefined'.
    // It can also be 'null', 'NaN' or a value of '0'. Converting
    // to Boolean is the suggested method that is used most of the
    // times because it covers all the aforementioned situations 
    // but if you only need to know if 'someVar' is undefined, 
    // then you should use JSValue.IsUndefined.
}
{% endhighlight %}
{% highlight vbnet %}
If Not CBool(someVar) Then
    ' 'someVar' is not a valid usable value or object but this 
    ' does not necessarily mean that 'someVar' is 'undefined'.
    ' It can also be 'null', 'NaN' or a value of '0'. Converting
    ' to Boolean is the suggested method that is used most of the
    ' times because it covers all the aforementioned situations 
    ' but if you only need to know if 'someVar' is undefined, 
    ' then you should use JSValue.IsUndefined.
End If
{% endhighlight %}

### JSObject Class

Objects in JavaScript are sort of like a managed `Dictionary` with key-value pairs where every key is of type `String` and every value can be any supported type, encapsulated in managed code by a `JSValue`.

As specified in the table above, these Objects are modeled in .NET with the [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject) class.

A `JSValue` that encapsulates a JavaScript `Object`, has [`JSValue.IsObject`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSValue_IsObject) set to `true` and can be implicitly casted to `JSObject`. For conversion details, see the JavaScript [`Object`](#object) section above.

There are two types of JSObjects, **Local** and **Remote**. 

#### Local JSObjects

Local objects only have properties (no custom methods) and are primarily used for declaring data to pass to methods or be returned from custom JavaScript method handlers. Local objects can be created using the parameterless [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject) [`constructor`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject__ctor):

{% highlight csharp %}
JSObject myObject = new JSObject();
myObject[ "name" ] = "Bob";
myObject[ "age" ] = 42;
{% endhighlight %}
{% highlight vbnet %}
Dim myObject As JSObject = New JSObject()
myObject("name") = "Bob"
myObject("age") = 42
{% endhighlight %}

> Local objects are treated as immultables that are passed to client-side by value. Once a local object is passed to the web-page, it becomes a remote object and **any changes to the original local object after the object is passed to the web-page, will not be reflected to the remote object and vice-versa**.

**Read [this article](local-jsobjects.html) for details of how to use Local JSObjects**.

You can also subclass `JSObject` to construct local objects with an interface that reflects standard features of the object. Awesomium.NET caches the original managed type of the wrapper and if the implementation of the subclass allows it, it will attempt to cast a local object that has been passed to the web-page, back to its original wrapper when the object is re-acquired or passed back to the hosting application. **For details, read: [How to: Create a Custom JSObject](custom-jsobjects.html)**.

#### Remote JSObjects

Remote objects live within the V8 engine in a separate process and can have both Properties (data accessors) and Methods (function members).

For example, say you had an object ‘Person’ in JavaScript:

{% highlight js %}
var Person = {
   name: 'Bob',
   age: 22,
};
{% endhighlight %}
	
You could then interact with this object in managed code:

{% highlight csharp %}
JSObject person = webView.ExecuteJavascriptWithResult( "Person" );

if( !person )
    return;

String name = person[ "name" ];
// Value of name is 'Bob'
     
int age = (int)person[ "age" ];
// Value of age is '22'
{% endhighlight %}
{% highlight vbnet %}
Dim person As JSObject = webView.ExecuteJavascriptWithResult("Person")

If Not CBool(person) Then Return

Dim name As String = person("name")
' Value of name is 'Bob'

Dim name As Integer = person("age")
' Value of age is '22'
{% endhighlight %}

#### Calling JavaScript Functions

You can call a JavaScript function directly via [`JSObject.Invoke`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Invoke) and [`JSObject.InvokeAsync`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_InvokeAsync).

Say you had the following functions in JavaScript:

{% highlight js %}
function getChatElement(id) {
  return document.getElementById(id);
}

function addChatMessage(chatElement, nickname, message) {
  chatElement.innerText = nickname + ': ' + message;
}
{% endhighlight %}

> Functions defined in the global scope, are actually function members (methods) of the global object, which is: [`window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) ![][external]. 
 
You can now call these functions directly from managed code like so:

{% highlight csharp %}
// Retrieve the global 'window' object from the page.
JSObject window = webView.ExecuteJavascriptWithResult( "window" );

if ( !window )
    return;

// Synchronously invoke |getChatElement| to get
// a DOM element where our message will be be shown.
JSObject chatElement = window.Invoke( "getChatElement", "chat" );

if ( !chatElement )
    return;

// Asynchronously post the message.
window.InvokeAsync( "addChatMessage", chatElement, "Bob", "Hello world!" );
{% endhighlight %}
{% highlight vbnet %}
' Retrieve the global 'window' object from the page.
Dim window As JSObject = webView.ExecuteJavascriptWithResult("window")
  
If Not CBool(window) Then Return

' Synchronously invoke |getChatElement| to get
' a DOM element where our message will be be shown.
Dim chatElement As JSObject = window.Invoke("getChatElement", "chat")

If Not CBool(chatElement) Then Return

' Asynchronously post the message.
window.InvokeAsync("addChatMessage", chatElement, "Bob", "Hello world!")
{% endhighlight %}

#### Lifetime of Objects

All managed JSObjects, local and remote, are disposable. Disposing the managed `JSObject` wrapper releases resources and destroys the native JSObject instance. Starting with **v.1.7.5**, all instances of `JSObject` created or acquired within a [Javascript Execution Context (JEC)](jec.html) (including JSObjects passed as arguments to a managed handler), are automatically disposed upon exiting the routine associated with the execution context.

**To make a copy of an object that persists outside a managed handler, use the [`Clone`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Clone) method**.

Remote JSObjects are reference-counted and collected by the V8 garbage collector. When you acquire or receive a JavaScript `Object` to the main process as `JSObject`, this ref-count is incremented, and when the `JSObject` is disposed, the ref-count is decremented.

You can think of remote JSObjects as a pointer to a V8 object on the page. Every time you make a copy of a `JSObject` (via [`Clone`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Clone)), only the reference ([remote ID](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSObject_RemoteId)) is copied; a deep copy is not made.

When the V8 object goes away (usually due to a page navigation), the reference will no longer be valid and method calls may fail. If you need a remote object to be available to all pages and persistent in V8 throughout the lifetime of the view, you should create a [Global JavaScript Object](#global_javascript_objects).

Local JSObjects are not associated with any web-view and web-page. You can think of them as an immutable dictionary of data that can be passed to multiple web-pages of different views by value. However local JSObjects should still be disposed and those created within a [Javascript Execution Context (JEC)](jec.html) are also automatically disposed upon exiting the routine associated with the execution context.

Just like remote JSObjects, When you make a copy of a local object (via [`Clone`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Clone)), only the reference to the internal dictionary of properties is copied and a deep copy is not made. Changes to the clone will affect the original and vice versa, until the object is passed to a remote web-page (see [Local Objects](#local_objects)).

**For more details and examples concerning the lifetime of JSObjects read**: 

* [Javascript Execution Context (JEC)](jec.html)
* [Handling Errors](#handling_errors)
* [Using Local JSObjects](local-jsobjects.html)

#### Dynamic Language Runtime

`JSObject` and any inheritors of it, support the **[Dynamic Language Runtime (DLR)](http://msdn.microsoft.com/en-us/library/dd233052.aspx)**. Support of the DLR adds dynamic features and behavior to the `JSObject` type in C# and Visual Basic that are statically typed languages, enabling easy and fast interoperation with JavaScript that is a dynamic language.

The `JSObject` type exposes a generic interface that allows you to access properties and invoke members on any kind of JavaScript object. JSObject's support of the DLR enables direct access of members of JavaScript objects using late binding. For example, you can use a `dynamic` `JSObject` to reference the HTML Document Object Model (DOM), which can contain any combination of valid HTML markup elements and attributes. Because each HTML document is unique, the members for a particular HTML document are determined at run time.

{% highlight csharp %}
Global global = Global.Current;

if ( !global )
    return;

var link = global.document.getElementById( "link_a" );

if ( !link )
    return;

link.click();
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

Dim js As [Global] = [Global].Current

If Not CBool(js) Then Return

Dim link = js.document.getElementById("link_a")

If Not link Then Return

link.click()
{% endhighlight %}

> When you are using JSObjects with the DLR inside a [Javascript Execution Context (JEC)](jec.html), binding errors that may occur are silently handled and propagated to V8 as [`TypeError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError) ![][external]. Also, created or acquired *dynamic* JSObjects are still automatically disposed upon exiting the routine associated with the execution context. For details, read: [Javascript Execution Context (JEC)](jec.html).

Using the DLR, you can also create custom JavaScript methods or even directly pass managed JavaScript method handlers as arguments to JavaScript methods in callback-based code.

**For details and examples, read the [Dynamic Language Runtime (DLR) Support](dlr.html) article**.

### Global Class

The [`Global`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Global) class provides access to essential objects of the currently loaded page's JavaScript environment, such as [`window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) and [`document`](https://developer.mozilla.org/en-US/docs/Web/API/document).

An instance of `Global` is available within every **asynchronous** [Javascript Execution Context (JEC)](jec.html) and it can either be accessed through the `Environment` property of the `EventArgs` of a JavaScript-related event (e.g. [`DocumentReadyEventArgs.Environment`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_Environment)), or through [`Global.Current`](http://docs.awesomium.net/?tc=P_Awesomium_Core_Global_Current).

> All JavaScript objects available through `Global`, are exposed as *dynamic*. This allows you to directly start working with them dynamically using the [Dynamic Language Runtime (DLR)](dlr.html).

The `Global` class implements an implicit conversion operator to `Boolean` which allows you to easily check the availability of an instance of `Global`.

Using `Global`, you avoid additional synchronous calls to the child process to acquire frequently used HTML Document Object Model (DOM) objects or built-in Global JavaScript objects:

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

    // Create a property descriptor.
	dynamic propConf = new JSObject();
    // We want it to be an accessor descriptor.
	// We create a synchronous anonymous remote 
	// Function and bind it to a managed handler.
	propConf.get = new JSFunction( webControl, (JavascriptMethodHandler)onReadOnlyGet );
	propConf.enumerable = true;

    // Define a read-only property to the prototype of Node,
	// the base class of all DOM elements. Accessing the property
	// will call our 'onReadyOnlyGet' handler.
	global.Object.defineProperty( global.window.Node.prototype, "myReadOnlyProperty", propConf );
}

private JSValue onReadOnlyGet( object sender, JavascriptMethodEventArgs e )
{
	return ( (IWebView)sender ).CreationTime.ToString();
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
	Dim js As [Global] = e.Environment

    If Not CBool(js) Then Return

	' Create a property descriptor.
	Dim propConf As Object = New JSObject()
	' We want it to be an accessor descriptor.
	' We create a synchronous anonymous remote 
	' Function and bind it to a managed handler.
	With propConf
		.get = New JSFunction(webControl, AddressOf onReadyOnlyGet)
		.enumerable = True
	End With

	' Define a read-only property to the prototype of Node,
	' the base class of all DOM elements. Accessing the property
	' will call our 'onReadyOnlyGet' handler.
	js.Object.defineProperty(js.window.Node.prototype, "myReadOnlyProperty", propConf)
End Sub

Private Function onReadyOnlyGet(sender As Object, e As JavascriptMethodEventArgs) As JSValue
	Return CType(sender, IWebView).CreationTime
End Function
{% endhighlight %}

## Declaring Custom Method Callbacks

Another nifty feature of Awesomium's JavaScript Integration, is that you can declare custom JavaScript methods that when they are invoked from JavaScript, they call a managed handler to the hosting application.

Just like [JavaScript-related events](#predefined_bindings), such methods can also be declared as *synchronous* or *asynchronous* (for details, read about [Synchronous & Asynchronous API](sync_async_js_api.html)).

You can declare custom JavaScript methods using both the regural CLR API of `JSObject` (see [`JSObject.Bind`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_JSObject_Bind) and [`JSObject.BindAsync`](http://docs.awesomium.net/?tc=Overload_Awesomium_Core_JSObject_BindAsync)), or the [Dynamic Language Runtime (DLR)](dlr.html).

### Examples

The following JavaScript code adds two listeners to the *`mouseover`* event of a `div` element. The listeners call custom methods available on the element that we add from managed code and are bound to managed handlers: 

{% highlight js %}
function onMouseOver(e) {
    // Do not pass the event object. This is called
    // synchronously and any objects passed will be 
    // stripped out.
    return this.onMouseOverHandler();
}

function onMouseOverAsync(e) {
    // You can pass objects to an asynchronous handler.
    this.onMouseOverAsyncHandler(e);
}

var myDiv = document.getElementById('myDiv');

if (myDiv) {
    // Add the asynchronous first because the
    // synchronous may cancel bubbling.
    myDiv.addEventListener('mouseover', onMouseOverAsync);
    myDiv.addEventListener('mouseover', onMouseOver);
}
{% endhighlight %}

#### CLR Example:

{% highlight csharp %}
private void OnDocumentReady( object sender, DocumentReadyEventArgs e )
{
	// When ReadyState is Ready, you can execute JavaScript against
	// the DOM but all resources are not yet loaded. Wait for Loaded.
	if ( e.ReadyState == DocumentReadyState.Ready )
		return;

    // We will add custom methods to the prototype of Node,
    // the base class of all DOM elements.
    JSObject nodePrototype = webView.ExecuteJavascriptWithResult( "Node.prototype" );

    if ( !nodePrototype )
        return;

    nodePrototype.Bind( "onMouseOverHandler", OnMouseOverHandler );
    nodePrototype.BindAsync( "onMouseOverAsyncHandler", OnMouseOverAsyncHandler );
}

private JSValue OnMouseOverHandler( object sender, JavascriptMethodEventArgs e )
{
    // Attempt to cancel bubbling.
	return false;
}
private void OnMouseOverAsyncHandler( object sender, JavascriptMethodEventArgs e )
{
    if ( e.Arguments.Length < 1 )
        return;

    JSObject mouseEvent = e.Arguments[ 0 ];

    if ( !mouseEvent )
        return;

    String message = String.Format( "Mouse over header at: {0}x{1}", 
        mouseEvent[ "clientX" ], mouseEvent[ "clientY" ] );

	Console.WriteLine( message );
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

Private Sub OnDocumentReady(sender As Object, e As DocumentReadyEventArgs)
	' When ReadyState is Ready, you can execute JavaScript against
	' the DOM but all resources are not yet loaded. Wait for Loaded.
	If e.ReadyState = DocumentReadyState.Ready Then Return

    ' We will add custom methods to the prototype of Node,
    ' the base class of all DOM elements.
	Dim nodePrototype As JSObject = webView.ExecuteJavascriptWithResult("Node.prototype")

    If Not CBool(nodePrototype) Then Return

	nodePrototype.Bind("onMouseOverHandler", AddressOf OnMouseOverHandler)
    nodePrototype.BindAsync("onMouseOverAsyncHandler", AddressOf OnMouseOverAsyncHandler)
End Sub

Private Function OnMouseOverHandler(sender As Object, e As JavascriptMethodEventArgs) As JSValue
    ' Attempt to cancel bubbling.
	Return False
End Function

Private Sub OnMouseOverAsyncHandler(sender As Object, e As JavascriptMethodEventArgs)
	If e.Arguments.Length < 1 Then Return

    Dim mouseEvent As JSObject = e.Arguments(0)

    If Not CBool(mouseEvent) Then Return

    Dim message As String = String.Format("Mouse over header at: {0}x{1}", _ 
        mouseEvent("clientX"), mouseEvent("clientY"))

	Console.WriteLine(message)
End Sub
{% endhighlight %}

#### DLR Example:

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

    // We will add custom methods to the prototype of Node,
    // the base class of all DOM elements.
    var nodePrototype = global.window.Node.prototype;

    if ( !nodePrototype )
        return;

    // We dynamically create properties, in this case of Function
    // type and we assign our managed handlers directly. Casting
    // to the proper delegate tells JSObject what kind of custom 
    // methods to create, synchronous or asynchronous.
    nodePrototype.onMouseOverHandler = 
        (JavascriptMethodHandler)OnMouseOverHandler;
    nodePrototype.onMouseOverAsyncHandler = 
        (JavascriptAsyncMethodHandler)OnMouseOverAsyncHandler;
}

private JSValue OnMouseOverHandler( object sender, JavascriptMethodEventArgs e )
{
    // Attempt to cancel bubbling.
	return false;
}
private void OnMouseOverAsyncHandler( object sender, JavascriptMethodEventArgs e )
{
    if ( e.Arguments.Length < 1 )
        return;

    dynamic mouseEvent = (JSObject)e.Arguments[ 0 ];

    if ( !mouseEvent )
        return;

    // Dynamically access the properties of the passed MouseEvent.
    String message = String.Format( "Mouse over header at: {0}x{1}", 
        mouseEvent.clientX, mouseEvent.clientY );

	Console.WriteLine( message );
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
	Dim js As [Global] = e.Environment

    If Not CBool(js) Then Return

    ' We will add custom methods to the prototype of Node,
    ' the base class of all DOM elements.
	Dim nodePrototype As Object = js.window.Node.prototype

    ' Note that when a JSObject is converted to a dynamic Object, 
    ' you no longer need to use CBool with the Not operator.
    If Not nodePrototype Then Return

    ' We dynamically create properties, in this case of Function
    ' type and we assign our managed handlers directly. Casting
    ' to the proper delegate tells JSObject what kind of custom 
    ' methods to create, synchronous or asynchronous.
	nodePrototype.onMouseOverHandler = _
        CType(AddressOf OnMouseOverHandler, JavascriptMethodHandler)
    nodePrototype.onMouseOverAsyncHandler = _
        CType(AddressOf OnMouseOverAsyncHandler, JavascriptAsyncMethodHandler)
End Sub

Private Function OnMouseOverHandler(sender As Object, e As JavascriptMethodEventArgs) As JSValue
    ' Attempt to cancel bubbling.
	Return False
End Function

Private Sub OnMouseOverAsyncHandler(sender As Object, e As JavascriptMethodEventArgs)
	If e.Arguments.Length < 1 Then Return

    Dim mouseEvent As Object = CType(e.Arguments(0), JSObject)

    If Not mouseEvent Then Return

    ' Dynamically access the properties of the passed MouseEvent.
    Dim message As String = String.Format("Mouse over header at: {0}x{1}", _ 
        mouseEvent.clientX, mouseEvent.clientY)

	Console.WriteLine(message)
End Sub
{% endhighlight %}

What's more, you can even create anonymous JavaScript methods using the public constructors of [`JSFunction`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction) or, when using the DLR, directly pass the delegate to a managed handler as argument to a remote `JSObject` method call to be used as a callback.

**See [this article](custom-javascript-methods.html) for more examples and all the available options for creating Custom JavaScript Methods**.

## Global JavaScript Objects

Sometimes you need objects to persist between pages (such as when exposing Application objects to a WebView). In such cases, you should use Global Javascript Objects.

> See [this article](using-global-javascript-objects.html) for more information on Global JavaScript Objects.

## Synchronous & Asynchronous API

Method calls to and from the web-page, are sent via a piped message to and from the child process. Such calls can be either *synchronous* or *asynchronous*.

Most managed handlers of [JavaScript-related events](#predefined_bindings) and of custom JavaScript methods (see [Declaring Custom Method Callbacks](#declaring_custom_method_callbacks)), are called in a **[Javascript Execution Context (JEC)](jec.html)**. Depending on how the handler is called, synchronously or asynchronously, a *synchronous* or *asynchronous* JEC is created.

For details, exammples as well as a list of benefits and limitations of both *synchronous* and *asynchronous* calls, read:

* **[Synchronous & Asynchronous API](sync_async_js_api.html)**
* **[Javascript Execution Contexts](jec.html)**

## Handling Errors

There are three (3) categories of errors that can occur when interacting with a remote page using JavaScript. These are:

* Native Errors
* JavaScript Errors
* Binding Errors (when using the [DLR](dlr.html))

### Native Errors

Method calls on Remote objects that return a value, are proxied to the child process and execute **_synchronously_**. These may fail for various reasons, that are described in the documentation of the **[`Error`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Error)** enumeration.

If the result value that you acquire (usually [`JSValue.Undefined`](http://docs.awesomium.net/?tc=F_Awesomium_Core_JSValue_Undefined)), indicates that such a call may have failed, you can check for a native error through:

* [`JSObject.GetLastError`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_GetLastError)
* [`IWebView.GetLastError`](http://docs.awesomium.net/?tc=M_Awesomium_Core_IWebView_GetLastError)

{% highlight csharp %}
JSObject myObject = webView.ExecuteJavascriptWithResult[ "myObject" ];
if ( !myObject && ( webView.GetLastError() != Error.None ) ) {
  // Handle error here (if any).
}
{% endhighlight %}
{% highlight vbnet %}
Dim myObject As JSObject = webView.ExecuteJavascriptWithResult("myObject")
If (Not myObject) AndAlso (webView.GetLastError() <> Error.None) Then
  ' Handle error here (if any).
End If
{% endhighlight %}

Likewise:

{% highlight csharp %}
JSValue foobar = myObject[ "foobar" ];
if ( foobar.IsUndefined && ( myObject.GetLastError() != Error.None ) ) {
  // Handle error here (if any).
}
{% endhighlight %}
{% highlight vbnet %}
Dim foobar As JSValue = myObject("foobar")
If foobar.IsUndefined AndAlso (myObject.GetLastError() <> Error.None) Then
  ' Handle error here (if any).
End If
{% endhighlight %}

### JavaScript Errors

JavaScript errors that may occur when you interact with the remote page or when client-side JavaScript code is executed on the remote page, are handled by the JavaScript engine and reported to the JavaScript console.

You can monitor such errors and messages by handling the **[`IWebView.ConsoleMessage`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_ConsoleMessage)** event.

{% highlight csharp %}
webView.ConsoleMessage += OnConsoleMessage;

[...]

// Any JavaScript errors or JavaScript |console.log| calls,
// will call this handler.
private void OnConsoleMessage( object sender, ConsoleMessageEventArgs e )
{
    System.Diagnostics.Debug.Print(
       String.Format( "[Line: {0}] {1}", e.LineNumber, e.Message ) );
}
{% endhighlight %}
{% highlight vbnet %}
AddHandler webView.ConsoleMessage, AddressOf OnConsoleMessage

[...]

' Any JavaScript errors or JavaScript |console.log| calls,
' will call this handler.
Private Sub OnConsoleMessage(sender As Object, e As ConsoleMessageEventArgs)
    System.Diagnostics.Debug.Print(
        String.Format("[Line: {0}] {1}", e.LineNumber, e.Message))
End Sub
{% endhighlight %}

### Binding Errors

When using [dynamic coding](#dynamic_language_runtime) and Awesomium.NET's support of the **[DLR](http://msdn.microsoft.com/en-us/library/dd233052.aspx)**, binding errors can occur in several cases as described below:

* The owning view of a [remote `JSObject`](#local_jsobjects) has crashed or is no longer *alive* (see: [`IsLive`](http://docs.awesomium.net/?tc=P_Awesomium_Core_IWebView_IsLive)).
* When a synchronous call fails because of a [native error](#native_errors).
* When a remote [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject) is no longer alive (it is disconnected, disposed or deleted on the client-side).
* When an illegal action is performed on an object (like attempting to invoke a method on a [local `JSObject`](#local_jsobjects)).
* When attempting to invoke a remote object's method that doesn't exist (only if our [Javascript Interoperation Framework (JIF)](#jif) has failed to load).
* When attempting to directly invoke an object that does not represent a JavaScript `function`.

> Depending on the programming language you use, the thrown Exception can be [`RuntimeBinderException`](https://msdn.microsoft.com/en-us/library/microsoft.csharp.runtimebinder.runtimebinderexception.aspx), [`MissingMemberException`](https://msdn.microsoft.com/en-us/library/system.missingmemberexception.aspx) etc.. Whenever possible, JSObject's and JSValue's support of the DLR, provides standard and documented CLR exceptions in response to binding errors (such as a [`NotSupportedException`](https://msdn.microsoft.com/en-us/library/system.notsupportedexception.aspx) for attempting to directly invoke an object that does not represent a JavaScript `function`).

{% highlight csharp %}
dynamic myObject = null;

try
{
	myObject = (JSObject)webView.ExecuteJavascriptWithResult( "myObject" );
	
	if ( !myObject )
	    return;
	
	using ( myObject ) {
	    // If the object has not a function member |myMethod|,
	    // this call will cause a binding error.
	    myObject.myMethod();
	    // The object does not represent a JavaScript |function|
	    // object. This will also cause a binding error.
	    myObject();
	}
}
catch ( Exception ex )
{
    // Since an exception here may actually be hiding a native
    // error, also check:
    if ( myObject && ( myObject.GetLastError() != Error.None ) ) {
        // Handle error here (if any).
    }
    if ( !myObject && ( webView.GetLastError() != Error.None ) ) {
        // Handle error here (if any).
    }
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

Dim myObject As Object = Nothing

Try
    myObject = CType(webView.ExecuteJavascriptWithResult("myObject"), JSObject)

    If Not myObject Then
        Return
    End If

    Using myObject
	    ' If the object has not a function member |myMethod|,
	    ' this call will cause a binding error.
        myObject.myMethod()
        ' This is prevented by the VB compiler itself.
        'myObject()
    End Using
Catch ex As Exception
    ' Since an exception here may actually be hiding a native
    ' error, also check:
    If myObject AndAlso
        (myObject.GetLastError() <> [Error].None) Then
        ' Handle error here (if any).
        Return
    End If
    If Not myObject AndAlso
        (webView.GetLastError() <> [Error].None) Then
        ' Handle error here (if any).
        Return
    End If
End Try
{% endhighlight %}

### Javascript Execution Context

All errors that may occur when your code is executed in a [Javascript Execution Context (JEC)](jec.html), are silently handled and propagated to the JavaScript console.

> The lifetime of JavaScript objects is also handled automatically in a Javascript Execution Context. Notice in the example below
> that you don't need to explicitly dispose instances of `JSObject` that are acquired in the JEC routine.
>
> For more details, read the **[Javascript Execution Context (JEC)](jec.html)** article.

Depending on the type of error and your code's flow, execution in the routine executed in a JEC may continue or may be interrupted:

{% highlight csharp %}
dynamic myObject = (JSObject)webControl.ExecuteJavascriptWithResult( "myObject" );

if ( !myObject )
    return;

// If the object has not function member |myMethod|,
// this call will cause an error but if JIF is loaded
// it's handled in JavaScript and managed execution continues.
myObject.myMethod();
// The object does not represent a JavaScript |function|
// object. This will also cause an error but if JIF is loaded
// it's handled in JavaScript and managed execution continues.
myObject();

JSObject myLocalObject = new JSObject();
// This is not allowed on local objects.
myLocalObject.Bind( "myMethod", (JavascriptMethodHandler)onMyMethod );
// This will not be executed. The exception above has exited the JEC routine.
myLocalObject[ "myProperty" ] = 5;
{% endhighlight %}
{% highlight vbnet %}
Dim myObject As Object = CType(webControl.ExecuteJavascriptWithResult("myObject"), JSObject)

If Not myObject Then
    Return
End If

' If the object has not a function member |myMethod|,
' this call will cause a binding error.
myObject.myMethod()

' VB.NET's support of the DLR behaves differently than C#.
' The above error will exit the JEC routine.
{% endhighlight %}

## Additional Resources

For more details and examples about Javascript Integration in Awesomium.NET, read the following articles:

