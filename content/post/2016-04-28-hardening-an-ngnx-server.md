---
title: Hardening an ngnx Server
date: 2016-04-28
modified: 2018-03-17T10:10:16.114979-04:00
tags:
  - security
  - servers
categories:
  - programming
---

Besides the Clarkonium.net site where I host this blog, I have several other sites that I maintain. This is all more of an educational effort for myself than anything really important. That said, I am a neophyte in terms of server security. It's something I want to do better. What better place to start than by evaluating security settings on some of my "play" sites that use [nginx](http://nginx.org) as the server component?

Like I said, I'm no expert, but these are some of the things I have done to my own sites.

<!--more-->

### Start by Updating the Version of nginx ###

Many (most?) Linux distros install a version that is quite out of date and contains known vulnerabilities. Generally, newer versions have improved security. For example, as I write this, Ubuntu typically installs a version that is quite out of date and contains known vulnerabilities.

To fix this, download the latest <em>stable</em> version from the [nginx download site](http://nginx.org/en/download.html) and install it.

### Use a Tool to Check Security Settings of the Server ###

There's a [free service](https://www.htbridge.com/websec) that will check the security of any web server. For example, if you check the email service [RunBox](https://runbox.com/), you will find that it receives a grade of A+. If you install nginx on Ubuntu (14.04 LTS), even the current version, out of the box it will receive a grade of F. What can you do to improve the results? Here are some suggestions.

#### Hide the Server Version Number ####

This is related to updating the version of the server. Sending the version number can help an attacker use known vulnerabilities in that version. It also gives a skilled hacker the chance to develop a new attack specific to that version since they can study a specific version of the source code for the server.

You can tell nginx to stop sending version information by editing the `/etc/nginx/nginx.conf` file. (Depending on your distro, the configuration file might be in a different location.) In that file, find or add a line that says:

```shell
server_tokens off;
```

Then, back at the command line, re-start the server.

```shell
sudo service nginx restart
```

#### Send Appropriate Headers ####

A lot of the recommendations are related to the information sent from the server in the headers.

##### X-Frame-Options #####

This setting determines if a web page can be "framed" or not. Setting this option to one of the available settings can allow the server to defend against [clickjacking](https://www.owasp.org/index.php/Clickjacking).

Again, this can be controlled by simply editing the `/etc/nginx/nginx.conf` file. You can add the following line right below the `server_tokens` line added in the previous section.

```shell
add_header X-Frame-Options "SAMEORIGIN";
```

This setting will allow the page to be displayed in a frame with the same origin as the page.

##### X-XSS-Protection #####

Cross site scripting attacks are fairly common. A simple way to increase protection against them is to include an additional header item:

```shell
add_header X-Xss-Protection "1; mode=block"
```

The `1` tells the browser to use it's built-in cross site scripting protection heuristics. Most browsers these days have built-in defenses that can be activated this way. The `mode=block` part tells the browser to block the servers response if the heuristics have detected an XSS.

##### X-Content-Type-Options #####

It is possible for some browsers to "sniff" the content type. This setting can disable that type of detection and force the browser to use the declared content type. This can reduce exposure to drive-by download attacks on sites that serve user-uploaded content, like blog comments for example.
 
```shell
add_header X-Content-Type-Options nosniff;
```

##### Content-Security-Policy #####

Content-Security-Policy (CSP) settings let you specify explicitly the only sources that the server can download resources from, like scripts, styles, fonts and images. This setting can be rather complicated and may require some re-working of the web site. A good introduction can be found at [An Introduction to Content Security Policy](http://www.html5rocks.com/en/tutorials/security/content-security-policy/). I urge you to review that post or a similar one since they explain it much better than me.

Before you move to a strict policy, you can use a `Content-Security-Policy-Report` header to examine how a proposed policy is violated by an existing site. For example, you might include the following directive in the nginx configuration file.

```shell
add_header Content-Security-Policy-Report-Only "default-src 'self'"
add_header report-uri /my_base_reporting_page;
```

The `default-src 'self'` directive specifies that most resources must be loaded from the server. Note however that some resources do not fall back to the `default-src` and can be loaded from anywhere unless explicitly limited elsewhere. See the above mentioned article for a list of these resource types.

The version that I eventually used for my site was:

```shell
add_header Content-Security-Policy "default-src 'self'; script-src 'self' maxcdn.bootstrapcdn.com code.jquery.com; img-src 'self' data:; style-src 'self' https://themes.googleusercontent.com http://fonts.googleapis.com maxcdn.bootstrapcdn.com cdnjs.cloudflare.com; font-src 'self' http://fonts.gstatic.com data:; child-src 'self'; connect-src 'self' https://apis.google.com; object-src 'none' ";
```

You should read the post mentioned above for details, but this long line does several things **for my specific site**. First it only allows scripts to be loaded from the sites specified and from the site the server is running on. It *does not* allow scripts to be embedded in the HTML served up by the site. Likewise, embedded styles are not allowed in the HTML -- they must be loaded from a CSS file, whether internal or from one of the external sites listed.

Fonts were a little tricky for me. The site uses two [Google fonts](https://www.google.com/fonts). The `head` section of the page HTML loads them like this:

```html
<link href='http://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,400,700' rel='stylesheet' type='text/css'>
<link href='http://fonts.googleapis.com/css?family=Libre+Baskerville:400italic,700italic,400,700' rel='stylesheet' type='text/css'>
```

But, in addition to the reference to `fonts.googleapis.com` in the `style-src` section of the CSP, the references to `themes.googleusercontent.com` was required in the `style-src` and the reference to `fonts.gstatic.com` was required in the `font-src` section. I don't really know why. I just fiddled around based on the error messages in the Firefox developer console until I got it to work.

So there you have it. The way one guy fiddled with his site content and configuration in an effort to improve security. When analyzed by the [High-Tech Bridge Free Web Server Security Test](https://www.htbridge.com/websec/), the analysis now comes back with an A+ and no warnings. I know there is more to do, but this is a start.
