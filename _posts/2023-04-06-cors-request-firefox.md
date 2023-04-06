---
layout: post
title: CORS Request Failing in Firefox but not Chrome
---

**The error**

While developing websites I test in both Chromium and Firefox. I recently ran into a problem where VM launch requests from the DevOpsChops website frontend would always work in Chromium, but would occasionally fail in Firefox. A VM launch request here is an AJAX POST request where a user clicks a button, then waits 3-5 seconds for a VM to be launched and then they get a shell on the VM.

Specifically, the error appearing in the browser console was: "The Same Origin Policy disallows reading the remote resource at http://127.0.0.1:8888/. (Reason: CORS request did not succeed). Status code: (null)"

![CORS request failing in Firefox](/public/cors-firefox.png)

I instinctively searched for the wording of the error, and all the answers were about broken CORS policies. I soon realized that the error I was seeing wasn't really to do with CORS. It was only reported as a CORS failure since it was a [complex request](https://ieftimov.com/posts/deep-dive-cors-history-how-it-works-best-practices/#preflight-requests) (POST request with JSON body) that never got a response. The real issue was not receiving a response from the server.

So why was I getting a server response in Chromium but not in Firefox? A clue was that this error occurred more often when my CPU was busy. This was related to timing in some way.

**The solution**

Further research led to a Mozilla Bugzilla report ["Empty response from POST AJAX request from Datatables"](https://bugzilla.mozilla.org/show_bug.cgi?id=1602154), where similar behaviour was occurring on POST requests to an interactive tables site, although the response was empty rather than throwing a CORS error. The root cause is that Firefox has a configuration setting `network.http.network-changed.timeout` which is set to 5 by default. Network requests which are inactive for over 5 seconds are killed.

When I increased this setting in `about:config` I no longer faced the error. My long-term fix has been for the DevOpsChops server to keep the connection alive by pushing partial responses until the full response can be sent.

**Future of Firefox**

But, it does make me think that behaviour like this is part of the reason for Firefox's decline. Chrome is more tolerant of badly-written websites and the problem with DataTables was never resolved. Many Firefox users won't bother to figure out why a website won't work and will simply switch to Chrome. Some technical websites like [KodeKloud](https://kodekloud.com) recommend only using Chrome because it's easier for them than spending the effort on making sure their site is fully compatible.
