---
layout: page
title : Important Changes in 1.7.5
group: Changelogs
weight: 1

---

[external]: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAKCAYAAACNMs+9AAAAVklEQVR4Xn3PgQkAMQhDUXfqTu7kTtkpd5RA8AInfArtQ2iRXFWT2QedAfttj2FsPIOE1eCOlEuoWWjgzYaB/IkeGOrxXhqB+uA9Bfcm0lAZuh+YIeAD+cAqSz4kCMUAAAAASUVORK5CYII=

<p class="intro">This article presents important changes in version <b>1.7.5</b> that require refactoring your code.</p>

#### For a full list of new features and changes in version 1.7.5, read: [What's New in 1.7.5](whats-new-1-7-5.html).

## Breaking Changes

A list of changes in version 1.7.5 that require refactoring your code:

### 1. [`JSObject.Bind(...,JavascriptMethodEventHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_4) is obsolete.

The **obsolete** [`JSObject.Bind`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_4) was used to create a custom JavaScript method and bind a managed handler to it. The same method was used to create both *synchronous* and *asynchronous* methods that were bound to a [`JavascriptMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMethodEventHandler).

Version 1.7.5 introduces new, more efficient methods to create and handle custom JavaScript methods using either the regular [Awesomium.NET CLR API](../javascript/custom-javascript-methods.html) or the **[Dynamic Language Runtime (DLR)](../javascript/dlr.html)**.

Together with the old [`JSObject.Bind`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_4), **the following types and members are also obsolete**:

* **[`JavascriptMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMethodEventHandler)**
* **[`JavascriptMethodEventArgs.Result`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_Result)**

The new regular API includes four (4) methods to create *synchronous* JavaScript methods and another four (4) to create *asynchronous* methods:

#### Create Synchronous JavaScript Methods:

* [`JSObject.Bind(JavascriptMethodHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind)

    The new [`JavascriptMethodHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptMethodHandler) returns a `JSValue` directly from the handler. You no longer need to check [`JavascriptMethodEventArgs.MustReturnValue`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_MustReturnValue) since a `JavascriptMethodHandler` can only handle a *synchronounous* JavaScript method and you cannot use [`JavascriptMethodEventArgs.Result`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_Result) (which is also **obsolete**) since the result is returned directly from the `JavascriptMethodHandler` method.

    > In this overload of `JSObject.Bind`, the name of the new custom JavaScript method added to the JavaScript *`Object`*, is determined by the name of the managed handler. Therefore, you cannot pass a method with a special name (such as an anonymous method or a lambda expression) to this overload (see [documentation](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind)).
* [`JSObject.Bind(JSFunctionHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_1)

    [`JSFunctionHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunctionHandler) is a new type of method for handling *synchronous* JavaScript methods. Like `JavascriptMethodHandler` it also returns a result directly from the handler but unlike `JavascriptMethodHandler`, `JSFunctionHandler` only accepts an array of `JSValue` as arguments. This array is equivalent to [`JavascriptMethodEventArgs.Arguments`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_Arguments) passed to a `JavascriptMethodHandler`. This new method signature simplifies code and follows the JavaScript pattern.

    > This overload of `JSObject.Bind`, also determines the name of the new custom JavaScript method, from the name of the managed handler (see above).
* [`JSObject.Bind(String,JavascriptMethodHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_2)

    This is similar to `JSObject.Bind(JavascriptMethodHandler)`, but users can define the name of the new custom JavaScript method added to the JavaScript *`Object`*. This overload allows you to pass methods with a special name (such as an anonymous method or a lambda expression).
* [`JSObject.Bind(String,JSFunctionHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_Bind_3)

    This is similar to `JSObject.Bind(JSFunctionHandler)`, but users can define the name of the new custom JavaScript method added to the JavaScript *`Object`*. This overload allows you to pass methods with a special name (such as an anonymous method or a lambda expression).

#### Create Asynchronous JavaScript Methods:

* [`JSObject.BindAsync(JavascriptAsyncMethodHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_BindAsync)

    The new [`JavascriptAsyncMethodHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptAsyncMethodHandler) does not return a value (returns *`void`*). You no longer need to check [`JavascriptMethodEventArgs.MustReturnValue`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_MustReturnValue) since a `JavascriptAsyncMethodHandler` can only handle an *asynchronounous* JavaScript method.

    > In this overload of `JSObject.BindAsync`, the name of the new custom JavaScript method added to the JavaScript *`Object`*, is determined by the name of the managed handler. Therefore, you cannot pass a method with a special name (such as an anonymous method or a lambda expression) to this overload.
* [`JSObject.BindAsync(JSFunctionAsyncHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_BindAsync_1)

    [`JSFunctionAsyncHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunctionAsyncHandler) is a new type of method for handling *asynchronous* JavaScript methods. Like `JavascriptAsyncMethodHandler` it does not return a result but unlike `JavascriptAsyncMethodHandler`, `JSFunctionAsyncHandler` only accepts an array of `JSValue` as arguments. This array is equivalent to [`JavascriptMethodEventArgs.Arguments`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JavascriptMethodEventArgs_Arguments) passed to a `JavascriptAsyncMethodHandler`. This new method signature simplifies code and follows the JavaScript pattern.

    > This overload of `JSObject.BindAsync`, also determines the name of the new custom JavaScript method, from the name of the managed handler (see above).
* [`JSObject.BindAsync(String,JavascriptAsyncMethodHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_BindAsync_2)

    This is similar to `JSObject.BindAsync(JavascriptAsyncMethodHandler)`, but users can define the name of the new custom JavaScript method added to the JavaScript *`Object`*. This overload allows you to pass methods with a special name (such as an anonymous method or a lambda expression).
* [`JSObject.BindAsync(String,JSFunctionAsyncHandler)`](http://docs.awesomium.net/?tc=M_Awesomium_Core_JSObject_BindAsync_3)

    This is similar to `JSObject.BindAsync(JSFunctionAsyncHandler)`, but users can define the name of the new custom JavaScript method added to the JavaScript *`Object`*. This overload allows you to pass methods with a special name (such as an anonymous method or a lambda expression).

#### Refactoring Example (CLR)

##### Old Method:

{% highlight csharp %}
using ( JSObject myGlobalObject = webView.CreateGlobalJavascriptObject( 
    "myGlobalObject" ) )
{
    myGlobalObject.Bind( "myMethod", true, OnMyMethod );
    myGlobalObject.Bind( "myAsyncMethod", false, OnMyMethod );
}

private void OnMyMethod( object sender, JavascriptMethodEventArgs e )
{
    if ( !e.MustReturnValue )
        return;

    e.Result = "My response";
}
{% endhighlight %}
{% highlight vbnet %}
Using myGlobalObject As JSObject = webView.CreateGlobalJavascriptObject(
    "myGlobalObject")
    myGlobalObject.Bind("myMethod", True, AddressOf OnMyMethod)
    myGlobalObject.Bind("myAsyncMethod", False, AddressOf OnMyMethod)
End Using

Private Sub OnMyMethod(sender As Object, e As JavascriptMethodEventArgs)
    If Not e.MustReturnValue Then Return

    e.Result = "My response"
End Sub
{% endhighlight %}

##### New Method:

{% highlight csharp %}
using ( JSObject myGlobalObject = webView.CreateGlobalJavascriptObject(
    "myGlobalObject" ) )
{
    // The method name is determined from the passed handler's name.
    // Therefore we cannot pass a lambda expression to these overloads.
    myGlobalObject.Bind( myMethod );
    myGlobalObject.BindAsync( myAsyncMethod );
}

private JSValue myMethod( object sender, JavascriptMethodEventArgs e )
{
    // Return result directly from the handler.
    return "My response";
}

// Demonstrating JSFunctionAsyncHandler.
private void myAsyncMethod( JSValue[] arguments )
{
    // The handler is called asynchronously and
    // it does not return a result.
}
{% endhighlight %}
{% highlight vbnet %}
Using myGlobalObject As JSObject = webView.CreateGlobalJavascriptObject(
    "myGlobalObject")
    myGlobalObject.Bind(myMethod)
    myGlobalObject.BindAsync(myAsyncMethod)
End Using

Private Function myMethod(sender As Object,
                          e As JavascriptMethodEventArgs) As JSValue
    ' Return result directly from the handler.
    Return "My response"
End Function

' Demonstrating JSFunctionAsyncHandler.
Private Sub myAsyncMethod(arguments() As JSValue)
    ' The handler is called asynchronously and
    ' it does not return a result.
End Sub
{% endhighlight %}

#### Refactoring Example (DLR)

##### Old Method:

{% highlight csharp %}
dynamic myGlobalObject = (JSObject)webView.ExecuteJavascriptWithResult(
    "myGlobalObject" );
 
// Make sure we have the object.
if ( myGlobalObject == null )
    return;
 
using ( myGlobalObject )
{
    myGlobalObject.myMethod = (JavascriptMethodEventHandler)myMethod;
    myGlobalObject.myAsyncMethod = (JavascriptAsynchMethodEventHandler)myMethod;
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...] 

Dim myGlobalObject As Object = CType(webView.ExecuteJavascriptWithResult(
    "myGlobalObject"), JSObject)
 
' Make sure we have the object. 
If myGlobalObject Is Nothing Then 
    Return 
End If
 
Using myGlobalObject
    myGlobalObject.myMethod = 
        CType(AddressOf OnCustomJavascriptMethod, JavascriptMethodEventHandler)
    myGlobalObject.myAsyncMethod = 
        CType(AddressOf OnCustomJavascriptMethod, JavascriptAsynchMethodEventHandler)
End Using
{% endhighlight %}

##### New Method:

{% highlight csharp %}
// All JSValues can now be implicitly casted to JSObject, even if they do not
// represent a JavaScript object. See Breaking Change N.3 below.
dynamic myGlobalObject = (JSObject)webView.ExecuteJavascriptWithResult(
    "myGlobalObject" );
 
// Invalid JSObjects are converted to false.
if ( !myGlobalObject )
    return;
 
using ( myGlobalObject )
{
    myGlobalObject.myMethod = (JavascriptMethodHandler)myMethod;
    myGlobalObject.myAsyncMethod = (JSFunctionAsyncHandler)myAsyncMethod;
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...] 

' All JSValues can now be implicitly casted to JSObject, even if they do not
' represent a JavaScript object. See Breaking Change N.3 below.
Dim myGlobalObject As Object = CType(webView.ExecuteJavascriptWithResult(
    "myGlobalObject"), JSObject)
 
' Invalid dynamic JSObjects are evaluated as False.
If Not myGlobalObject Then 
    Return
End If
 
Using myGlobalObject
    myGlobalObject.myMethod = 
        CType(AddressOf OnCustomJavascriptMethod, JavascriptMethodEventHandler)
    myGlobalObject.myAsyncMethod = 
        CType(AddressOf OnCustomJavascriptMethod, JSFunctionAsyncHandler)
End Using
{% endhighlight %}

#### Additional Resources:

* [Introduction to JavaScript Integration](../javascript/introduction.html)
* [Synchronous & Asynchronous API](../javascript/sync-async-js-api.html)
* [Create Custom JavaScript Methods](../javascript/custom-javascript-methods.html)

### 2. [`JavascriptAsynchMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptAsynchMethodEventHandler) is obsolete.

The **obsolete** [`JavascriptAsynchMethodEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JavascriptAsynchMethodEventHandler) was used with the **Dynamic Language Runtime (DLR)** to tell a `JSObject` to create an **_asynchronous_** custom JavaScript method and bind a managed handler to it.

Since there are now two (2) new method types for specifying *asynchronous* custom JavaScript method handlers, **`JavascriptAsynchMethodEventHandler` is deprecated and cannot be used any more**.

#### Additional Resources:

For examples of using the new available API, **see the refactoring example above and also read**:

* [Introduction to JavaScript Integration](../javascript/introduction.html)
* [Synchronous & Asynchronous API](../javascript/sync-async-js-api.html)
* [Create Custom JavaScript Methods](../javascript/custom-javascript-methods.html)

### 3. [`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue) is now a class.

Up until version 1.7.5, [`JSValue`](http://docs.awesomium.net/1_7_4/?tc=T_Awesomium_Core_JSValue) was a value type (*`struct`*). Starting with version 1.7.5, **[`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue) is now a reference type (*`class`*)**.

The following list explains some of the resons for this change:

* Many new features have been added to `JSValue` that could not be implemented if `JSValue` remained an immutable value type. Some of them are the indexer that allows editing JavaScript arrays directly from `JSValue` and the unary and binary operators.
* `JSValue`, even as it was before 1.7.5, did not qualify almost all of the **[requirements](http://msdn.microsoft.com/en-us/library/ms229017.aspx)** for remaining a structure. Beyond its size, the most important issue was that due to its extensive use with the **Dynamic Language Runtime (DLR)** supported by the Awesomium.NET Javascript Integration, `JSValue` was frequently being boxed/unboxed. Because boxes are objects that are allocated on the heap and are garbage-collected, too much boxing and unboxing can have a negative impact on the heap, the garbage collector, and ultimately the performance of the application. In contrast, no such boxing occurs as reference types are cast. [![][external]](http://www.informit.com/store/framework-design-guidelines-conventions-idioms-and-9780321545619)
* [`JSValue` is the type that all JavaScript values are being converted to](../javascript/introduction.html#jsvalue-class), when passed to and acquired from V8 or assigned to `JSObject` properties. Reference type assignments copy the reference, whereas value type assignments copy the entire value. Therefore, assignments of large reference types are cheaper than assignments of large value types, as `JSValue` was. [![][external]](http://www.informit.com/store/framework-design-guidelines-conventions-idioms-and-9780321545619)

#### Refactoring:

**This change should not affect user code in most scenrarios**. 

* In the rare scenario that your code used CLR's default parameterless constructror available with all value types, this code will have to be refactored since `JSValue`, as a reference type, does no contain a parameterless constructor:
	
##### Old Method:

{% highlight csharp %}
// Create a local object.
JSObject myObject = new JSObject();
// Create a JavaScript value, using the parameterless constructor.
// This would create an uninitialized JSValue that Awesomium.NET
// would translate to JSValue.Undefined.
JSValue myValue = new JSValue();
// Would assign 'undefined' to 'myProperty'.
myObject[ "myProperty" ] = myValue;
{% endhighlight %}
{% highlight vbnet %}
' Create a local object.
Dim myObject As JSObject = New JSObject()
' Create a JavaScript value, using the parameterless constructor.
' This would create an uninitialized JSValue that Awesomium.NET
' would translate to JSValue.Undefined.
Dim myValue As JSValue = New JSValue()
' Would assign 'undefined' to 'myProperty'.
myObject("myProperty") = myValue
{% endhighlight %}

##### New Method:

{% highlight csharp %}
// Create a local object.
JSObject myObject = new JSObject();
// There's no parameterless constructor to JSValue.
// You must explicitly specify the value. 
JSValue myValue = JSValue.Undefined;
// Will assign 'undefined' to 'myProperty'.
myObject[ "myProperty" ] = myValue;
{% endhighlight %}
{% highlight vbnet %}
' Create a local object.
Dim myObject As JSObject = New JSObject()
' There's no parameterless constructor to JSValue.
' You must explicitly specify the value. 
Dim myValue As JSValue = JSValue.Undefined
' Will assign 'undefined' to 'myProperty'.
myObject("myProperty") = myValue
{% endhighlight %}

* As a reference type, JSValue is now *nullable*. Assigning or passing a null `JSValue` reference, will effectively assign or pass `JSValue.Null`:

{% highlight csharp %}
// Create a local object.
JSObject myObject = new JSObject();
// Will assign 'null' to 'myProperty'.
myObject[ "myProperty" ] = null;
{% endhighlight %}
{% highlight vbnet %}
' Create a local object.
Dim myObject As JSObject = New JSObject()
' Will assign 'null' to 'myProperty'.
myObject("myProperty") = Nothing
{% endhighlight %}

* A null `JSValue` reference can also be implicitly converted to `Boolean`:

{% highlight csharp %}
JSValue myValue = null;

// Will be 'false'
if ( !myValue )
  return;
{% endhighlight %}
{% highlight vbnet %}
Dim myValue As JSValue = Nothing

' Will be 'false'
if Not CBool(myValue) Then Return
{% endhighlight %}


> **All members of the Awesomium.NET Javascript Integration API, are guaranteed to never return a null JSValue reference**. In the worse case, they will return `JSValue.Undefined`.

#### Additional Resources:

* [Introduction to JavaScript Integration](../javascript/introduction.html)

### 4. All [`JSValue`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSValue) instances, can be implicitly converted to [`JSObject`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObject).

Due to changes in Awesomium.NET's support of the Dynamic Language Runatime (DLR), **all `JSValue` instances can now be implicitly converted to `JSObject`** (or predefined subclasses of it such as [`JSFunction`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSFunction)), **even if they do not represent a JavaScript `Object`** (or `Function`).

Until version 1.7.5, attempting to cast a `JSValue` that does not represent a JavaScript `Object` to `JSObject`, would return a null `JSObject` reference. Starting with version 1.7.5, this casting will return an *invalid* `JSObject` instance ([`JSObject.Type`](http://docs.awesomium.net/?tc=P_Awesomium_Core_JSObject_Type) is [`Invalid`](http://docs.awesomium.net/?tc=T_Awesomium_Core_JSObjectType)) that is equivalent to `JSValue.Undefined`. This is mainly because new equality and conversion operators were added to both `JSValue` and `JSObject` that simplify code expecially when using the **Dynamic Language Runtime (DLR)**. When such an *invalid* `JSObject` is checked against `null`, it will return `true` and when converted to `Boolean`, it will be converted to `false`:

The following example explains why this feature is added and how you can use the new operators:

{% highlight csharp %}
// The returned value will be a JSValue of type Integer. However casting to
// JSObject will still succeed returning an invalid JSObject equivalent to
// JSValue.Undefined.
dynamic myObject = (JSObject)webView.ExecuteJavascriptWithResult( "0" );
// There's a new operator that converts JSObjects to Boolean. An invalid
// JSObject will be converted to false. Notice that the result above, is
// assigned to a weakly typed variable (dynamic). If casting to JSObject
// would assign 'null' to 'myObject' the CLR/DLR would not be able to know
// which type's operators to invoke to convert the value to Boolean and the
// following condition would fail with a message like:
// "Cannot convert null to Boolean".
if ( !myObject )
    return;
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

' The returned value will be a JSValue of type Integer. However casting to
' JSObject will still succeed returning an invalid JSObject equivalent to
' JSValue.Undefined.
Dim myObject = CType(webView.ExecuteJavascriptWithResult("0"), JSObject)
' There's a new operator that converts JSObjects to Boolean. An invalid
' JSObject will be converted to false. Notice that the result above, is
' assigned to a weakly typed variable (dynamic). If casting to JSObject
' would assign 'null' to 'myObject' the CLR/DLR would not be able to know
' which type's operators to invoke to convert the value to Boolean and the
' following condition would fail with a message like:
' "Cannot convert Nothing to Boolean" or "Cannot find a Not unary operator
' for Nothing".
If Not myObject Then Return
{% endhighlight %}

#### Refactoring:

**This change actually simpilfies code and it should not affect user code in most scenrarios**. However, in the rare scenario that your code uses other methods than simple equality operations to check if a `JSObject` equals to `null`, these methods may produce unexpected results:

Here's a summary of the new features and potentially needed refactoring:

{% highlight csharp %}
JSObject myObject = null;
// A strongly typed (JSObject) variable that has been assigned a null reference,
// will be successfully converted to Boolean. The CLR knows to invoke the static
// operators of JSObject:
if ( myObject )
{
    // Will never reach here. 'myObject' is null so it's converted to 'false'.
    return;    
}

// The returned value will be a JSValue of type Integer. However casting to
// JSObject will still succeed returning an invalid JSObject equivalent to
// JSValue.Undefined.
myObject = webView.ExecuteJavascriptWithResult( "0" );

// When an invalid JSObject is checked against 'null', it will return 'true':
if ( myObject != null )
{
    // Will never reach here. 'myObject' is not actually a null reference
    // but it is invalid.
    return;
}

if ( !Object.ReferenceEquals( myObject, null ) )
{
    // HOWEVER THIS CODE WILL BE REACHED! Operator overloads are not 
    // called for methods such as 'Object.ReferenceEquals'. Although 
    // invalid, 'myObject' is not actually a null reference.
    System.Diagnostics.Debug.Print( "Reached Here" );
}
{% endhighlight %}
{% highlight vbnet %}
Dim myObject As JSObject = Nothing
' A strongly typed (JSObject) variable that has been assigned a null reference,
' will be successfully converted to Boolean. The CLR knows to invoke the static
' operators of JSObject:
If CBool(myObject) Then
    ' Will never reach here. 'myObject' is null so it's converted to 'False'.
    Return    
End If

' The returned value will be a JSValue of type Integer. However casting to
' JSObject will still succeed returning an invalid JSObject equivalent to
' JSValue.Undefined.
myObject = webView.ExecuteJavascriptWithResult("0")

' When an invalid JSObject is checked against 'null', it will return 'true':
If myObject IsNot Nothing Then
    ' Will never reach here. 'myObject' is not actually a null reference
    ' but it is invalid.
    Return
End If

If Not Object.ReferenceEquals(myObject, Nothing) Then
    ' HOWEVER THIS CODE WILL BE REACHED! Operator overloads are not 
    ' called for methods such as 'Object.ReferenceEquals'. Although 
    ' invalid, 'myObject' is not actually a null reference.
    System.Diagnostics.Debug.Print("Reached Here")
End If
{% endhighlight %}

#### Additional Resources:

* [Introduction to JavaScript Integration](../javascript/introduction.html)


## Important Changes

The following changes do not require refactoring your code but your application will perform better and your code can be simplified by taking these changes into consideration.

### 1. The behavior and signature of the [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event, has changed.

* Until version 1.7.5, a [`UrlEventHandler`](http://docs.awesomium.net/1_7_4/?tc=T_Awesomium_Core_UrlEventHandler) method was used to handle a [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event. **Starting with version 1.7.5, the new [`DocumentReadyEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventHandler) method is used to handle a [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event**.

  [`DocumentReadyEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventHandler) accepts a [`DocumentReadyEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventArgs) as event arguments that provide data for the event. `DocumentReadyEventArgs` still derives [`UrlEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_UrlEventArgs) so old `UrlEventHandler` methods assigned as handlers of a `DocumentReady` event will keep on working but user code will not have access to many new features provided by the new event handler arguments.

* Starting with version 1.7.5, the [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event may be fired more than once when a web-page is being loaded (or edited through JavaScript). `DocumentReady` is fired every time the *[ready-state](https://developer.mozilla.org/en-US/docs/Web/API/document.readyState)* of the loaded page changes and the new [`DocumentReadyEventArgs`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventArgs) passed to your [`DocumentReadyEventHandler`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyEventHandler) method, contains a [`ReadyState`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_ReadyState) property that informs you if the DOM is still being loaded or if it is completely loaded.

  Until version 1.7.5, users used to handle the [`LoadingFrameComplete`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_LoadingFrameComplete) waiting for [`IsMainFrame`](http://docs.awesomium.net/?tc=P_Awesomium_Core_FrameEventArgs_IsMainFrame) to be `true` before executing JavaScript that required access to the page's DOM. This was because as noted, `DocumentReady` was being fired too early (it was equivalent to [`DOMContentLoaded`](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded)). This technique is now not suggested and it is actually discouraged for the following reasons:

  * `DocumentReady` is now fired more than once and it is guaranteed to fire when the DOM is completely loaded and ready ([`ReadyState`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_ReadyState) will be [`Loaded`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyState)).
  * `LoadingFrameComplete` is not fired in a **[Javascript Execution Context (JEC)](../javascript/jec.html)** since it is not a Javascript-related event. Features available to methods executed in a JEC, are not available to a `LoadingFrameComplete` event handler.
  * The *main frame* of a page (see: [`IsMainFrame`](http://docs.awesomium.net/?tc=P_Awesomium_Core_FrameEventArgs_IsMainFrame)) is usually the last to load when a page is being loaded but this is not guaranteed.
  * A page that has all its HTML content loaded, doesn't necessarily have a completely loaded DOM. A view created with JavaScript *`window.open`* specifying no target URL, does not have a ready DOM until JavaScript code loads something into the new *`window`* or uses *`document.write`*, *`document.close`*.

* Handlers of a [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event are now executed in an *asynchronous* **[Javascript Execution Context (JEC)](../javascript/jec.html)**. This technology is new in version 1.7.5. Methods executed in a JEC, enjoy certain benefits such as:

  * `JSObject` instances created or acquired in a JEC, do not need to be explicitly disposed. They are automatically disposed upon exiting the method associated with the JEC.
  * Errors or exceptions that occur within a JEC, are silently handled and propagated to the [JavaScript console](http://docs.awesomium.net/?tc=E_Awesomium_Core_WebView_ConsoleMessage).
  * Code executing in an *asynchronous* JEC, has immediate access to essential objects of the loaded page's current JavaScript environment (such as *`window`* and *`document`*) through the new [`Global`](http://docs.awesomium.net/?tc=T_Awesomium_Core_Global) class, without any need to perform additional synchronous calls to the child-process to acquire these objects. In particular, the [`DocumentReadyEventArgs.Environment`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_Environment) property provides an instance of `Global` when [`DocumentReadyEventArgs.ReadyState`](http://docs.awesomium.net/?tc=P_Awesomium_Core_DocumentReadyEventArgs_ReadyState) is [`Loaded`](http://docs.awesomium.net/?tc=T_Awesomium_Core_DocumentReadyState).

#### Refactoring:

**It is strongly suggested that you revise your code taking advantage of the new features provided to the [`DocumentReady`](http://docs.awesomium.net/?tc=E_Awesomium_Core_IWebView_DocumentReady) event**. The following example shows how the `DocumentReady` can be handled:

{% highlight csharp %}
webView.DocumentReady += OnDocumentReady;

[...]

private void OnDocumentReady( Object sender, DocumentReadyEventArgs e )
{
    // If your code needs to access and manipulate the DOM,
    // wait for it be fully loaded.
    if ( e.ReadyState == DocumentReadyState.Ready )
        return;

    // DOM is fully loaded. Access essential objects
    // through Global.
    var global = e.Environment;

    // Implicit casting to Boolean available.
    if ( !global )
        return;

    // Create a local object whose own properties specify data 
    // property descriptors to be added to a remote object, 
    // with the corresponding property names and values.
    var props = new JSObject();
    // Specify the property name, the data property descriptor and initial value.
    props[ "myProperty",
           new JSPropertyDescriptor() { Writable = false } ] = "Ready";
    // Create a remote object using generics of the global Object and
    // assign it to a global variable. Note that members of the Global
    // class, are exposed as 'dynamic'.
    global.window.myVar = global.Object.create( global.Object.prototype, props ); 

    // More examples.
    var myDiv = global.document.getElementById( "myDiv" );

    if ( !myDiv )
        return;

    myDiv.innerHTML = "Loaded";

    // Note that we do not not explcitly dispose any JSObject
    // (by wrapping code in 'using' statements for example).
    // All JSObjects created or acquired in a JEC, are
    // automatically disposed upon exiting the method.
}
{% endhighlight %}
{% highlight vbnet %}
Option Explicit Off

[...]

AddHandler webView.DocumentReady, AddressOf OnDocumentReady

[...]

Private Sub OnDocumentReady(sender As Object, e As DocumentReadyEventArgs)
    ' If your code needs to access and manipulate the DOM,
    ' wait for it be fully loaded.
    If e.ReadyState = DocumentReadyState.Ready Then Return

    ' DOM is fully loaded. Access essential objects
    ' through Global.
    var js = e.Environment;

    ' Implicit casting to Boolean available.
    If Not CBool(global) Then Return

    ' Create a local object whose own properties specify data 
    ' property descriptors to be added to a remote object, 
    ' with the corresponding property names and values.
    Dim props As New JSObject()
    ' Specify the property name, the data property descriptor and initial value.
    props("myProperty",
          New JSPropertyDescriptor() With { .Writable = false }) = "Ready"
    ' Create a remote object using generics of the global Object and
    ' assign it to a global variable. Note that members of the Global
    ' class, are exposed as 'dynamic'.
    global.window.myVar = global.Object.create(global.Object.prototype, props)

    ' More examples.
    Dim myDiv = global.document.getElementById("myDiv")

    If Not myDiv Then Return

    myDiv.innerHTML = "Loaded"

    ' Note that we do not not explcitly dispose any JSObject
    ' (by wrapping code in 'Using' statements for example).
    ' All JSObjects created or acquired in a JEC, are
    ' automatically disposed upon exiting the method.
End Sub
{% endhighlight %}

#### Additional Resources:

* [Introduction to JavaScript Integration](../javascript/introduction.html)
* [Javascript Execution Context](../javascript/jec.html)

