ring.middleware.logger
======================

Ring middleware to log the duration and other details of each request.

The logging backend is pluggable, and defaults to clojure.tools.logging
if none is given.

This is ALPHA software; the internals and API can change at any time.
Pull requests are welcome!

Usage
-----

In your `project.clj`, add the following dependency:

    [ring.middleware.logger "0.5.0-SNAPSHOT"]


Then, just add the middleware to your stack. It comes preconfigured with
reasonable defaults, which append ANSI colorized log messages on each
request to whatever logger is in use by clojure.tools.logging.

    (ns foo
      (:require [ring.adapter.jetty     :as jetty]
                [ring.middleware.logger :as logger]))

    (defn my-ring-app [request]
         {:status 200
          :headers {"Content-Type" "text/html"}
          :body "Hello world!"})
          
    (jetty/run-jetty (logger/wrap-with-logger my-ring-app) {:port 8080})


If you'd prefer plaintext logging without the ANSI colors, use
`wrap-with-plaintext-logger` instead.


Custom Logger Backend
-----------------------

You can supply a map of custom logger functions to `wrap-with-logger`,
which will be used instead of the default `clojure.tools.logging`
functions. The default map is:

      {:info  (fn [x] (clojure.tools.logging/info x))
       :debug (fn [x] (clojure.tools.logging/debug x))
       :error (fn [x] (clojure.tools.logging/error x))
       :warn  (fn [x] (clojure.tools.logging/warn x))}
       
Replace these functions with whatever logging facility you'd like to
use. Each function should take a string and log it at that log level.


What Gets Logged
----------------

The default setup logs:

* an :info-level message when a request begins;
* an :info level message when a response is generated without any server
errors (i.e. its HTTP status is < 500);
* an :error level message when a response's HTTP status is >= 500;
* an :error level message with a stack trace when an exception is thrown during response generation.

All messages are timestamped. Each request is assigned a random
4-hex-digit ID, so that different log messages pertaining to the same
request can be cross-referenced. These IDs are printed in random ANSI colors
by default, for easy visual correlation of log messages while reading
a log file.



License
-------
Copyright (C) 2012-2014 Paul Legato.
Distributed under the Eclipse Public License, the same as Clojure.
