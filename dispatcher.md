---
layout: default
title: Webmachine request dispatching
---
# Webmachine request dispatching</h3>

This page describes the configuration of URI dispatch to resources
in a webmachine application. The dispatch map data structure is a list
of 3-tuples, where each entry is of the form `{pathspec, resource,
args}`. The first pathspec in the list that matches the URI for a
request will cause the corresponding resource to be used in handling
that request.

A `pathspec` is a list of pathterms. A pathterm is any of
[string,atom,star] where star is just the atom of "\*". The
pathspec-matching is done by breaking up the request URI into tokens
via the "/" separator and matching those tokens against the
pathterms. A string pathterm will match a token if the token is equal
to that string. A non-star atom will match any single token. The star
atom (\* in single quotes) will match any number of tokens, but may
only be present as the last pathterm in a pathspec. If all tokens are
matched and all pathterms are used, then the pathspec matches.  The
tokens used are available in `wrq:path_tokens(ReqData)`
in the resource functions.

Any atom pathterms that were used in a match will cause a binding in
the path_info element of the request's
<a href="reqdata.html">ReqData</a>.  If
there was a `foo` atom that matched the token
`"bar"`, then `wrq:path_info(foo, ReqData)` will
return `"bar"` inside the resource calls, and in any case
`wrq:path_info(ReqData)` will return a Dict term with all
the bindings, accessible via the `dict` standard library
module.  If there was a star pathterm in the pathspec, then
`wrq:disp_path(ReqData)` in a resource function will return
the URI portion that was matched by the star.

The `resource` is an atom identifying a
<a href="resources.html">resource</a> that
should handle a matching request. It will have the `args`
(which must be a list) passed to its init function before request
handling begins.

In the default directory structure for a new webmachine application,
the dispatch terms will be in file:consult form in
`priv/dispatch.conf` under the application root.

### Examples
The examples below are taken from
[Justin Sheehy's slide at Erlang Factory 2009](http://www.erlang-factory.com/conference/SFBayAreaErlangFactory2009/speakers/justinsheehy)

| Dispatch Rule                        | URL      | wrq:disp_path | wrq:path   | wrq:path_info | wrq:path_tokens |
| ------------------------------------ | -------- | ------------- | ---------- | ------------- | --------------- |
|{["a"], some_resource, []}            | /a       | ""            | "/a"       | []            | []              |
|{["a", '\*'], some_resource, []}      | /a       | ""            | "/a"       | []            | []              |
|{["a", '\*'], some_resource, []}      | /a/b/c   | "b/c"         | "/a/b/c"   | []            | ["b", "c"]      |
|{["a", foo], some_resource, []}       | /a/b     | ""            | "/a/b"     | [{foo, "b"}]  | []              |
|{["a", foo, '\*'], some_resource, []} | /a/b     | ""            | "/a/b"     | [{foo, "b"}]  | []              |
|{["a", foo, '\*'], some_resource, []} | /a/b/c/d | "c/d"         | "/a/b/c/d" | [{foo, "b"}]  | ["c", "d"]      |

Query strings are easy too:

* Given rule: {["a", foo, '\*'], some_resource, []}
* And URL: /a/b/c/d?fee=ah&amp;fie=ha
* Then wrq:get_qs_value("fie",ReqData) -&gt; "ha"
