---
layout: default
title: Webmachine request/response data
---
# Webmachine request/response data

This is documentation of the Webmachine Request Data API as
embodied by the `"wrq"`module.  This module is the
means by which resources access and manipulate the state of
the request they are handling.

Given that all webmachine resource functions have this
signature:

{% highlight erlang %}
f(ReqData, Context) -> {Result, ReqData, Context}
{% endhighlight %}

we should explain in detail the `ReqData`input and output
parameter.  This is a data structure used to represent the request
sent by the client as well as the response being built by the
resource.  The `wrq`module is used to access the values in
the input parameter.  Most functions in most resources have no need to
modify the output `ReqData`and can simply pass along the
one received as input.  However, in some cases a resource will need to
make some update to the response other than that implied by
`Result`and in those cases it should use the
`wrq`module to build a
modified `ReqData`from the original one for the
return value.

A couple of nonstandard types are assumed here:

| Type | Description |
| ---- | ----------- |
| string() | a list() with all elements in the ASCII range |
| rd() | opaque record, used as the input/output `ReqData` |
| streambody() | A webmachine [streamed body format](streambody.html) |
| mochiheaders() | a structure used in mochiweb for HTTP header storage |

The accessors are:

| Function | Description |
| ---- | ----------- |
| `method(rd()) -&gt; 'DELETE' | 'GET' | 'HEAD' | 'OPTIONS' | 'POST' | 'PUT' | 'TRACE' ` | The HTTP method used by the client.  (note that it is an `atom() `) |
| `version(rd()) -&gt; {integer(),integer()} ` | The HTTP version used by the client.  Most often `{1,1} `. |
| `peer(rd()) -&gt; string() ` | The IP address of the client. |
| `disp_path(rd()) -&gt; string() ` | The "local" path of the resource URI; the part after any prefix used in [dispatch configuration](dispatcher.html).  Of the three path accessors, this is the one you usually want.  This is also the one that will change after `create_path`is called in your [resource](resources.html). |
| `path(rd()) -&gt; string() ` | The path part of the URI -- after the host and port, but not including any query string. |
| `raw_path(rd()) -&gt; string() ` | The entire path part of the URI, including any query string present. |
| `path_info(atom(),rd()) -&gt; 'undefined' | string() ` | Looks up a binding as described in [dispatch configuration](dispatcher.html). |
| `path_info(rd()) -&gt; any() ` | The dictionary of bindings as described in [dispatch configuration](dispatcher.html). |
| `path_tokens(rd()) -&gt; list() ` | This is a list of `string() `terms, the disp_path components split by "/". |
| `get_req_header(string(),rd()) -&gt; 'undefined' | string() ` | Look up the value of an incoming request header. |
| `req_headers(rd()) -&gt; mochiheaders() ` | The incoming HTTP headers.  Generally, get_req_header is more useful. |
| `req_body(rd()) -&gt; 'undefined' | binary() ` | The incoming request body, if any. |
| `stream_req_body(rd(),integer()) -&gt; streambody() ` | The incoming request body in [streamed](streambody.html) form, with hunks no bigger than the integer argument. |
| `get_cookie_value(string(),rd()) -&gt; string() ` | Look up the named value in the incoming request cookie header. |
| `req_cookie(rd()) -&gt; string() ` | The raw value of the cookie header.  Note that get_cookie_value is often more useful. |
| `get_qs_value(string(),rd()) -&gt; 'undefined' | string() ` | Given the name of a key, look up the corresponding value in the query string. |
| `get_qs_value(string(),string(),rd()) -&gt; string() ` | Given the name of a key and a default value if not present, look up the corresponding value in the query string. |
| `req_qs(rd()) -&gt; [{string(), string()}] ` | The parsed query string, if any.  Note that get_qs_value is often more useful. |
| `get_resp_header(string(),rd()) -&gt; string() ` | Look up the current value of an outgoing request header. |
| `resp_redirect(rd()) -&gt; bool() ` | the last value passed to do_redirect, false otherwise -- if true, then some responses will be 303 instead of 2xx where applicable |
| `resp_headers(rd()) -&gt; mochiheaders() ` | The outgoing HTTP headers.  Generally, get_resp_header is more useful. |
| `resp_body(rd()) -&gt; 'undefined' | binary() ` | The outgoing response body, if one has been set.  Usually, append_to_resp_body is the best way to set this. |
| `app_root(rd()) -&gt; string() ` | Indicates the "height" above the requested URI that this resource is dispatched from.  Typical values are `"." `, `".." `, `"../.." `and so on. |


The functions for (nondestructive) modification of `rd() `terms are:

| Function | Description |
| ---- | ----------- |
| `set_resp_header(string(),string(),rd()) -&gt; rd() ` | Given a header name and value, set an outgoing request header to that value. |
| `append_to_resp_body(binary(),rd()) -&gt; rd() ` | Append the given value to the body of the outgoing response. |
| `do_redirect(bool(),rd()) -&gt; rd() ` | see resp_redirect; this sets that value. |
| `set_disp_path(string(),rd()) -&gt; rd() ` | The disp_path is the only path that can be changed during a request.  This function will do so. |
| `set_req_body(binary(),rd()) -&gt; rd() ` | Replace the incoming request body with this for the rest of the processing. |
| `set_resp_body(binary(),rd()) -&gt; rd() ` | Set the outgoing response body to this value. |
| `set_resp_body(streambody(),rd()) -&gt; rd() ` | Use this [streamed body](streambody.html) to produce the outgoing response body on demand. |
| `set_resp_headers([{string(),string()}],rd()) -&gt; rd() ` | Given a list of two-tuples of {headername,value}, set those outgoing response headers. |
| `remove_resp_header(string(),rd()) -&gt; rd() ` | Remove the named outgoing response header. |
