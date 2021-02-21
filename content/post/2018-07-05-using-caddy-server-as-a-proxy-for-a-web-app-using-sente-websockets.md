---
author: david
title: Using Caddy Server as a Proxy for a Web App using Sente WebSockets
date: 2018-07-04T09:49:16.376-04:00
modified: 2018-07-05T16:07:02.517-04:00
tags:
  - bugs
  - debugging
---


I created a simple web app, called [clajistan](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/clajistan/tree/default).

### Purpose ###

The purpose of creating the app was two-fold.

* I wanted to have a little app to act as a "placeholder" on a very lightly used virtual server.
* To learn how to use [WebSockets](https://en.wikipedia.org/wiki/WebSocket) ([sente](https://github.com/ptaoussanis/sente)) behind a proxy server ([Caddy](https://caddyserver.com)).

### Desired Performance ###

 The program displays a simple web page, written in ClojureScript, with a few statistics about the server, written in Clojure:

* The time and date when the server started.
* The current date and time.
* How long the server has been running.
* How many visitors have viewed the page.

All of the information, except for the current time, is sent from the server to the client. The current time and server uptime update every second.

The program works as expected when executed on _via_ REPL, from terminals, and compiling and running an uberjar.

To deploy, I followed these steps.

1. `lein do clean, uberjar` to build the jar file.
2. Copy the uberjar to the server with `scp -P 1037 target/clajistan.jar david@clajistan.com:~/clajistan.jar`.
3. When logged into the server, start the app running with `java -jar clajistan.jar > clajistan.log &`.
4. Start the caddy server with `caddy &`. 

### Issues ###

- The program correctly worked when running locally and accessed on `localhost`.
- WebSocket messages from the server to the client failed when deploying the app to a virtual server.
   * Only the initial, default values were shown for the fields that were to be populated by data from the WebSocket messages.
   * Opening the debugging console of the web server (Safari) showed several messages of the form `WebSocket connection to 'wss://clajistan.com/ws?client-id=4a99ec7a-9d15-45f9-9579-02d7727a1d0d' failed: Unexpected response code: 200`. Note that the error message indicates that the WebSocket protocol (`ws://...`) has been promoted to the secure WebSocket protocol (`wss://...`) as expected.

### Other Relevant Facts ###

The `Caddyfile` used to configure the Caddy server contained:

```bash
clajistan.com {
    proxy / http://localhost:1357
    log access.log
    errors error.log
    gzip
}
```

These are the same contents that were used to proxy a previous version of the program written before WebSockets were added.

The firewall (ufw) has open ports at address 1357 for TCP.

There are no obvious errors in the log files `clajistan.log` or `error.log`, Caddy's log file. Caddy's access log, `access.log` _does_ contain messages indicating that WebSocket messages were returning the unexpected return codes.

### The Fix ###

After a day of "googling" (really "duck-duck-going"),​ I couldn't find anything that seemed relevant but finally did find something interesting. As usual, it was a configuration problem due to my general ignorance. As a result, I made a small change to the `Caddyfile`.

```bash
clajistan.com {
    proxy / http://localhost:1357 {
        websocket
        transparent
    }
    log access.log
    errors error.log
    gzip
}
```
Note the addition of `websocket` and `transparent` to the `proxy` statement. That seemed to get things working when connecting with Firefox. Eventually, Safari began to work correctly as well.

That was one of the hardest things about getting this all to work. Safari seems to remember too much stuff. Clearing the cache and reloading the page is not always enough to bring changes into the displayed page. In this regard, Firefox seems to work better.​

### Remaining Issues ###

It seems like the socket connection will not remain active for a single session for more than a few hours. After that, Sente starts throwing errors, ​and the display stops updating. I have no idea why but the connection can be re-established by reloading the page.