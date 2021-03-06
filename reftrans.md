---
layout: default
title: Webmachine's approach to resource functions and referential transparency
---
# Webmachine's approach to resource functions and referential transparency

Webmachine goes to great lengths to help
your [resource functions](resources.html) to be as
referentially transparent as possible.  By "referentially
transparent" we mean that given the same input
`{ReqData, Context} `the function will return the same
output `{Result, ReqData, Context} `and that side
effects will be insignificant from the point of view of
Webmachine's execution.

We don't try to force you into pure referential transparency;
we give you as big a hole as you want
via `Context`.  As that term is
application-specific, you can put database handles, server
process identifiers, or anything else you like in there and we
won't try to stop you.

However, all Webmachine really cares about is the rest of the
terms.  Since resource functions are generally referentially
transparent at least with regard to those terms, many things
are easier -- testing, [debugging](debugging.html),
and even static analysis and reasoning about your Web
application.

Note that there is one important exception to this.
The [streamed body feature](streambody.html) exists
to allow resources to consume or produce request/response
bodies a hunk at a time without ever having the whole thing in
memory.  While the continuation-passing style used in the
streaming API is friendly to general functional analysis, due
to the necessary side-effect of reading or writing to sockets
the stream bodies cannot be treated in quite the same way as
other uses of the `ReqData`interface.  Luckily, it
is easy to inspect a `ReqData`to see if this is
the case in any individual resource or request instance.
