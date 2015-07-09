# Lebara Android JavaScript interface #

This document describes JavaScript interface which should be implemented in Lebara Android apps. This interface may be used then by a web development team to interact with native code, allowing app to handle and/or report web events. Android OS provides [simple API](http://developer.android.com/guide/webapps/webview.html#BindingJavaScript) which allows to inject Java objects into a page's JavaScript context, so that they can be accessed by JavaScript in the page.

## Native part ##
Java binding object must implement a method with the following signature:

```public void onWebEvent(String event, String eventArgs)```
where ```event``` is an event name and ```eventArgs``` is a JSON string. 

Java binding object must be registered in ```WebView``` using the [```addJavascriptInterface```](http://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)) with ```"LebaraJavascriptInterface"``` name.

Reference interface may look like this:

```java
import android.support.annotation.Nullable;

/**
 * Reference Lebara JavaScript interface.
 * Created by skif on 08/07/2015.
 */
public abstract class LebaraJavascriptInterface {

    /** The name used to expose the object in JavaScript. */
    public static final String NAME = "LebaraJavascriptInterface";

    /**
     * Invoked from JavaScript context.
     * @param event event name
     * @param eventArgs event arguments as JSON string
     */
    public abstract void onWebEvent(@Nullable String event, @Nullable String eventArgs);

}
```
Apps that target a ```JELLY_BEAN_MR1 (API level 17)``` version, must annotate implementation of this (and all other methods which should be accessible from JavaScript context) with [```@JavascriptInterface```](http://developer.android.com/reference/android/webkit/JavascriptInterface.html) annotation. 

## Web part ##
The following snippet shows how a web application can access the binding object:
```html
<script type="text/javascript">
    function reportEvent(event, data) {
        LebaraJavascriptInterface.onWebEvent(event, JSON.stringify(data));
    }
</script>
```
where ```event``` is string representing event name and ```data``` is a JavaScript object representing event arguments.

## Event name and arguments format ##
Event name is a plain string, must be non-null. All events with null or empty event name should be ignored. Event arguments may be null. If arguments are non-null, then the JSON string representing the arguments must be parseable into a map of plain string key-value pairs. In other words, objects and arrays are not allowed inside a JSON object.
