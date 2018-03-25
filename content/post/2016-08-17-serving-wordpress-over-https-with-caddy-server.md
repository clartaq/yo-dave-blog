---
title: Serving WordPress over HTTPS with Caddy Server
tags:
  - blogging
  - caddy
  - wordpress
date: 2016-08-19 18:02:38
modified: 2018-03-17T12:25:41.998139-04:00
---

The [Caddy](https://caddyserver.com/) server is a relatively new, easy-to-use server written in the [Go programming language](https://golang.org/). It is very buzzword compliant. But, the nicest thing about it, in my opinion, is that it sets up SSL/TLS ([Secure Sockets Layer](http://info.ssl.com/article.aspx?id=10241)/[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) certificates automatically from [Let's Encrypt](https://letsencrypt.org/) to let you serve a site with[ HTTPS](https://en.wikipedia.org/wiki/HTTPS) by default. The project is open source and the certificates are free.

Of course, [WordPress](https://wordpress.org/) is a very popular blogging platform. Caddy provides some [guidance](https://github.com/caddyserver/examples/tree/master/wordpress) on getting WordPress up and running on the server. However, it wasn't enough for me. Here's some step-by-step guidance on how I got it working on some of my own self-hosted sites.

<!--more-->

# Some Preliminaries #

Starting from a freshly imaged and updated Ubuntu 16.04 installation, do the following.

```bash
mkdir downloads
mkdir wordpress
```

Increase the number of files that the operating system can open. Caddy complains if the limit is below 8K. I have run with a lower limit; you probably can too. I just wanted to get rid of the nag. To do so, you need to edit a configuration file

```bash
sudo nano /etc/security/limits.conf
```

Then add the following two lines near the bottom of the file:

```shell
*   soft    nofile    8192
*   hard    nofile    8192
```

Reboot to make the changes effective.

# Install Caddy #

With the preliminaries out of the way, it's time to install Caddy. Caddy can be installed with many optional packages. Read the docs. For our purposes, a "plain vanilla" install will work fine.

## Download and Install ##

```bash
curl https:/getcaddy.com | bash
```

The installation script will figure out the OS and architecture to use then build and download the appropriate version. On my system it looked something like this:

```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4801  100  4801    0     0    133      0  0:00:36  0:00:35  0:00:01   133
Downloading Caddy for linux/amd64...
https://caddyserver.com/download/build?os=linux&arch=amd64&arm=&features=
Extracting...
Putting caddy in /usr/local/bin (may require password)
[sudo] password for user:
Caddy 0.9.0
Successfully installed
```

As you can see, you will need to supply your `sudo` password near the end of the installation process. You can verify the installation location of the program like this.

```bash
which caddy
/usr/local/bin/caddy
```

## Set Up a Caddyfile ##

Caddy operation is controlled by a configuration file, just like most other servers. There is plenty of documentation of the options on the Caddy site itself. For now, create a Caddy file in your home directory and edit it.

```bash
nano Caddyfile
```

Make it look like this.

```bash
www.example.com {
    redir https://example.com{uri}
}

example.com {
    root wordpress
    log access.log
    errors error.log

  gzip

  header / {
    # Enable HTTP Strict Transport Security (HSTS) to force clients to always
    # connect via HTTPS (not recommended if only experimenting with HTTPS)
    Strict-Transport-Security  "max-age=31536000;"
    # Enable cross-site filter (XSS) and tell browser to block detected attacks
    X-XSS-Protection "1; mode=block"
    # Prevent some browsers from MIME-sniffing a response away from the declared Content-Type
    X-Content-Type-Options "nosniff"
    # Disallow the site to be rendered within a frame (clickjacking protection)
    X-Frame-Options "DENY"
    # Disallow loading of resources from other sites
#    Content-Security-Policy "default-src 'self'; script-src 'self' maxcdn.bootstrapcdn.com code.jq$
  }

    # PHP-FPM with Unix socket
    fastcgi / /var/run/php/php7.0-fpm.sock php

    # PHP-FPM with regular udp socket
#    fastcgi / 127.0.0.1:9000 php

    # Routing for WordPress
    rewrite / {
        to {path} {path}/ /index.php?{query}
    }
}
```

This Caddyfile is based on one I found [here](https://denbeke.be/blog/servers/caddy-server-and-wordpress-php-fpm/). It is pretty obvious what is happening here.

*   On your system, replace "example.com" with your domain name.
*   The root directory of the served site is in the directory `wordpress`.
*   The server will log accesses and errors to the files mentioned.
*   The group of headers are just some standards used to protect the site from some kinds of attacks.

The very long, commented out `Content-Security-Policy` header restricts the source of resources like scripts, images, fonts, etc. that can be used by the site. It is just a starting point that can be altered later. As it is, it will interfere with the normal operation of WordPress. Hence, it is commented out.

`The X-Frame-Options` header may cause you some issues too, depending on how you use the WordPress dashboard. It prevents contents of your site from being displayed in a frame on another HTML page. Some commands in the WordPress dashboard try to do that. If you have issues, just comment out the line in the Caddyfile.

The rest hints at things to come that will be discussed later.

## Add a Simple Home Page ##

Create a home page for the site.

```bash
nano wordpress/index.html
```

Add the following contents to the file:

```html
<!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>My Caddy Site</title>
        </head>
        <body>
            <h1>Here's the Site</h1>
            <p>Pretty neat, huh?</p>
        </body>
    </html>
```

We plan to launch WordPress and the server as a "normal" user, that is, one without root privileges. However, Linux typically does not allow user-level programs to listen to ports below 1024\. There are several ways this could be handled, for example, run the server configured to listen to higher ports. That's inconvenient in my opinion. One way to allow a user level program to listen on ports 80 and 443 (HTTP and HTTPS) is to use the `setcap` program.

```bash
sudo setcap cap_net_bind_service=+ep /usr/local/bin/caddy
```

# Start It Up #

You're now ready to start Caddy and serve your site. When you start Caddy for the first time, you will be asked to enter your email address to aid in account recovery if it is ever needed later.

```bash
caddy
Activating privacy features...
Your sites will be served over HTTPS automatically using Let's Encrypt.
By continuing, you agree to the Let's Encrypt Subscriber Agreement at:
  https://letsencrypt.org/documents/LE-SA-v1.0.1-July-27-2015.pdf
Please enter your email address so you can recover your account if needed.
You can leave it blank, but you'll lose the ability to recover your account.
Email address: Lost in Translation
Activating privacy features... done.
https://www.example.com
https://example.com
http://www.example.com
http://example.com
```

If you point your browser to your site you should see the little web page we created above. But the exciting part is the little green lock icon that should be present next to the URL indicating you've successfully generated and retrieved SSL/TLS certificates from Let's Encrypt.

When I use the Firefox web console to monitor retrieval of the page, there are no errors or warnings. When I shut down the server (`Ctrl-C`), an new file, `access.log`, has appeared in my home directory containing details about the recent accesses from my browser. There is no `errors.log` file, indicating that caddy did not detect any errors. Woohoo!

So, that should be working. If you experience any errors, fix them before moving on.

# Install MariaDB #

WordPress requires a database to operate. The [requirements page](https://wordpress.org/about/requirements/) lists two possible choices.

*   [MySQL](https://www.mysql.com/) version 5.6 or greater
*   [MariaDB](https://mariadb.org/) version 10.0 or greater

I didn't know much about MariaDB, so I looked into it a little. It's a drop in replacement for MySQL from the same folks who originally developed MySQL. So..., let's give it a try.

Since MariaDB is not in the Ubuntu package repositories, the [MariaDB site](https://downloads.mariadb.org/mariadb/repositories/#mirror=jmu) will walk you through how to get it. These are the commands suggested to install MariaDB on my server.

```bash
sudo apt install software-properties-common
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
sudo add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://mirror.jmu.edu/pub/mariadb/repo/10.1/ubuntu xenial main'
sudo apt update 
sudo apt install mariadb-server
```

You should use their tool to get the latest stable version for your distro and architecture.

The installation will take several minutes. During installation, you will be asked to provide a root password for the database. Keep it somewhere safe.

After installation is complete, you should be able to do a` ps aux | grep 'mysql'` and see a `mysql` process somewhere in the list of running processes.

### Let's Create the WordPress Database ###

Now is a fine time to create the database that WordPress will use. Log into the MariaDB shell.

```bash
mysql -u root -p
```

You will be prompted to enter the database root password you created when installing MariaDB. Next, enter the following contents at the shell prompt.

```bash
CREATE DATABASE wordpress;
CREATE USER wordpressuser@localhost;
SET PASSWORD FOR wordpressuser@localhost= PASSWORD("password");
GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
exit
```

After each entry you will receive a confirmation message that is not shown here. Those messages should start with"`Query OK`".

This creates a database named `wordpress` and a user of that database named `wordpressuser`. The user will have the password `password`. Of course, use something unique for your own password. Don't forget, you need to enter it in two places.

# Install PHP #

This is where I had the most difficulty.

You can show which package will be installed by default by typing

```bash
sudo apt policy php
```

On my system, this output

```bash
php:
 Installed: (none)
 Candidate: 1:7.0+35ubuntu6
 Version table:
 *** 1:7.0+35ubuntu6 500
        500 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 Packages
        500 http://us.archive.ubuntu.com/ubuntu xenial/main i386 Packages
        100 /var/lib/dpkg/status
```

I'm sure you know the drill by now.

```bash
sudo apt install php php7.0-mysqlnd
```

(Note: On Ubuntu 14.04, the command to install php will install the php**5** package. Doing so includes a lot of other stuff, like setting up a version of the apache server, which takes over the port you want to use to serve caddy. Different installation instructions for that version.)

I had to install the updated `php7.0-mysqlnd` package because WordPress would not operate with the version installed in the main package. Still don't know why.

You also need to add your user to the `www-data` group.

```bash
sudo usermod -a -G www-data username
```

*   Now, edit `/etc/php/7.0/fpm/pool.d/www.conf`.
*   Search (`Ctrl-W` on nano) for the line that says `user=www-data` and change it to `user=username` where you replace username with the name of the user on your system.
*   Reboot.

# Install WordPress

Download the latest version of the WordPress system, unzip it, and modify the configuration to reflect the choices you made when setting up the database.

```bash
cd downloads
wget https://wordpress.org/latest.tar.gz
cd ..
tar -xzvf downloads/latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
nano wordpress/wp-config.php
```

In the `wp-config.php` file, replace the database name, database user and user password with the values you set earlier. While the editor is open, go the block of code with the comment saying "Authentication Unique Keys and Salts&" and point your browser at the location mentioned ( https://api.wordpress.org/secret-key/1.1/salt/). When you open that page, it will display a code block with new, unique values you can use. Just cut and paste it right over the top of the pre-existing dummy values. Save and exit.

OK. You are ready for WordPress to start installing. Start Caddy again and point your browser at "`example.com/readme.html`", replacing "`example.com`" with your domain name. You should see a welcome message, including a section on the "Famous 5-minute install". In step 2, click the link to "`<span class="file">wp-admin/install.php</span>`".

A new page will appear prompting you to select your language.

After selecting a language, a new page will load with spaces to input a bunch of information like the site name (what WordPress displays) a root user name for WordPress, a password for that user (the installation program will suggest a strong one for you), and an email address.

After you've filled in the information and saved a copy of the password somewhere, click the button that says "Install WordPress";

In a few seconds, you should be shown a new page indicating success and telling you to log in with the new user name and password. Click the button that says "Log In";. Upon a successful log in, you will be taken to the WordPress dashboard for your site, ready to customize and start posting.

Since you used Caddy to serve the site, you should also see a green lock icon somewhere on your URL address line. Happy Posting!

Back in your SSH client, you will likely want to be able to log off your server, but leave Caddy and WordPress running. The [nohup](http://manpages.ubuntu.com/manpages/precise/man1/nohup.1.html) command is what you want. First stop the server you started earlier with the `caddy` command. Then enter the following command.

```bash
nohup caddy &
```

This will start the server in a separate process and allow you to log off.

This has been a long post, but the steps are pretty simple. I think using Caddy is actually easier to set up than the [nginx](https://nginx.org/en/) or [Apache](https://httpd.apache.org/) servers. And so far, MariaDB has been working without complaint.

Perhaps you're wondering why this site isn't running on Caddy yet. The reasons are long and arcane and have nothing to do with the technology. Perhaps some day soon though.

There' plenty to write about, so tell people what you think and know.