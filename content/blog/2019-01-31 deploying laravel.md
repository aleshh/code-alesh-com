---
title: "Deploying a Laravel application to DreamHost shared hosting, 2018 edition"
date: "2019-01-31"
tags: ["PHP", "Laravel"]
---

<img class="alignright size-medium wp-image-606" src="http://buildingsandfood.com/wp-content/uploads/2018/03/laravel-from-scratch-300x199.jpg" alt="" width="300" height="199" />Unfortunately, this sort of information changes regularly. Google "dreamhost laravel" and you'll get some articles from a few years ago which, while helpful, were not sufficient for me. A few examples:

<ul>
 	<li><a href="http://yottaram.com/blog/2014/12/08/setting-up-a-laravel-application-on-a-dreamhost-shared-hosting-account/">Setting up a Laravel application on a DreamHost shared hosting account</a></li>
 	<li><a href="http://blog.netgloo.com/2016/01/29/deploy-laravel-application-on-shared-hosting/">Deploy Laravel 5 application on shared hosting</a></li>
 	<li><a href="https://medium.com/laravel-news/the-simple-guide-to-deploy-laravel-5-application-on-shared-hosting-1a8d0aee923e">The simple guide to deploy Laravel 5 application on shared hosting</a></li>
</ul>
Shockingly, DH's Knowledge Base is <a href="https://help.dreamhost.com/hc/en-us/search?utf8=✓&amp;query=laravel">silent</a> about Laravel. Here's what ended up working for me. If you're trying to follow these instructions and something is unclear or does not work, please <a href="http://twitter.com/alesh">reach out</a>.

<!--more-->

<h3>Step 1: Add a domain</h3>
In the DH panel, click <code>Manage Domains</code> and <code>Add Hosting to a Domain/Sub-domain</code>. I bet it's possible to get this working in a directory of an existing domain, but DH lets you add unlimited subdomains so why not create a new one for your app? Under Web directory, add <code>/public</code> to the end of the domain name. Set PHP mode to <code>7.1 FastCGI</code>. You'll probably also want to select <code>HTTPS</code> and <code>Create a New User</code>.
<h3>Step 2: Create a database</h3>
Click <code>Goodies</code> and <code>MySQL Databases</code>. Scroll to the bottom of the page to <code>Create a new MySQL database</code>. Type in a database name. Note down the name, the hostname, and the user. Click <code>Add new database</code>. Then find the database in the list (or any other database in there that has the same user) and click on the user's name. Click <code>Show</code> and note down the password. While you're in there, you should add your current computer's IP address to the list of allowable hosts; that'll allow you to inspect your database from your local machine with something like <a href="http://www.sequelpro.com">Sequel Pro</a>.
<h3>Step 3: Upload your files</h3>
You <a href="https://help.dreamhost.com/hc/en-us/articles/214899037-Installing-Composer-overview">can't run Composer</a> on a DH shared hosting plan, so upload your complete project from your local development environment, vendor folder and all. (Do not upload any files or folders that starts with a dot, like .env or .git. Also don't upload your <code>node_modules</code> folder, if you have one.)
<h3>Step 4: Set up .env</h3>
Copy the <code>.env</code> file from your project folder to a place you can edit it. (To see hidden files on a Mac, type <code>Command-Shift-.</code> in the Finer. Repeat the command when you're done.) Edit the lines for <code>DB_HOST</code>, <code>DB_DATABASE</code>, <code>DB_USERNAME</code>, and <code>DB_PASSWORD</code> with the info from step 2. Upload the file to the project root folder.
<h3>Step 5: Migrate your tables</h3>
SSH into your DH server: from your local command line, type <code>ssh -l [user] [domain]</code>. The user and domain are the ones you created in step 1, which is separate from the database user! CD into the root of the project folder. At this point, if you run <code>php -v</code> you will not get version 7.1 even though you selected it for the domain, go figure!

Type <code>php-7.1 artisan migrate</code>. This may work, but in my case, I got an error to the effect that <code>1071 Specified key was too long.</code> This issue <a href="https://github.com/laravel/framework/issues/17508">can be solved</a> by updating your <code>AppServiceProvider</code> file by adding <code>Schema::defaultStringLength(191);</code> to the <code>boot()</code> function and <code>use Illuminate\Support\Facades\Schema;</code> to the top. Now when you run <code>php-7.1 artisan migrate</code> you'll get a different error message, because you're trying to re-create tables that were created last time before the error?

You can probably do <code>php-7.1 artisan migrate:refresh</code> but this is where I logged into Sequel Pro and just deleted all the existing tables. At that point, <code>php-7.1 artisan migrate</code> worked, creating all my tables, and my application was working!

<h3>Done!</h3>
Again, if you're trying to follow these instructions and something is unclear or does not work, please <a href="http://twitter.com/alesh">let me know</a>.

<strong>Update:</strong> Lots of good details about this at the <a href="https://laracasts.com/discuss/channels/guides/deploying-a-laravel-application-to-dreamhost-shared-hosting">Laracasts thread for this post</a>.

This article was originally posted <a href="http://buildingsandfood.com/deploying-a-laravel-application-to-dreamhost-shared-hosting-2018-edition/">here</a>.
