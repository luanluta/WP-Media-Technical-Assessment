# WP-Media-Technical-Assessment
My technical assessment submission for the Technical Customer Support role at WP Media.

---

## Problem A

Hey there. I see that one of our users is looking for a way to clear the site’s HTML and Minified CSS/JavaScript files at a certain time each day. 

Whenever we want to automate a task like this we can set up a cron job to run code at a specific time. We have a full guide on this exact use case here:

https://docs.wp-rocket.me/article/494-how-to-clear-cache-via-cron-job

However, I’ve written both a “TL;DR” version of this process and a more detailed one explaining some of the thinking behind it.

Please let me know if anything is unclear, and I’d be happy to clarify.


## TL;DR

**1. Create a PHP file named "rocket-clean-domain.php" in the user's root WordPress folder containing the following code:**

```php
<?php 
// Load WordPress.
require( 'wp-load.php' );

// Clear cache.<br>// Also preload the cache if the Preload is enabled.
if ( function_exists( 'rocket_clean_domain' ) ) {
	rocket_clean_domain();
 }

// Clear minified CSS and JavaScript files.
if ( function_exists( 'rocket_clean_minify' ) ) {
	rocket_clean_minify();
}
```

Alternatively, you can download the file from this repo and use that.

**2. Stop WP-Cron from running by adding the following code to the user's wp-config.php file, above the line that says "That's all, stop editing! Happy publishing":**
```
	define('DISABLE_WP_CRON', true);
```

**3. Create a real cron job to run WP-Cron by running the following command:**

	wget -q -O - http://yourdomain.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1

**Note: Replace https://yourdomain.com with the actual URL of the user's website.**

**4. Add a cron job to run the rocket-clean-domain.php file by running the following command:**

```
/path/to/wp/installation/root/rocket-clean-comain.php
```
**Note: Replace /path/to/wp/installation/root with the actual path of the WordPress root folder.**

**5. Each server has its way of adding a cron job, so assistance may be required from the hosting provider, however, if you’re using CPanel, we have a video that shows them how to do it here:**

* [For the WP Cron cron job ( I would suggest they run it once per 5-10 minutes )](https://recordit.co/A4Jj1Kg7x9)
* [For the rocket-clean-comain.php cron job](https://recordit.co/cl2YCoMCzu)

---

## Detailed guide

This request can be split up into two smaller tasks:

* Write the code that will delete the files the user needs to be deleted.
* Setting up the site/server so that it runs the code that deletes the files at a certain time.


### The code 

Let’s take these one at a time. Firstly we need to write the code that deletes the files the user requested, and put that code into a PHP file that the server will run at the time the user chooses.

WP Rocket has functions available that will allow us to do this, namely the “rocket_clean_domain();” and “rocket_clean_minify();”. The code needed to achieve the user’s request is:

```php
<?php 
// Load WordPress.
require( 'wp-load.php' );

// Clear cache.<br>// Also preload the cache if the Preload is enabled.
if ( function_exists( 'rocket_clean_domain' ) ) {
	rocket_clean_domain();
 }

// Clear minified CSS and JavaScript files.
if ( function_exists( 'rocket_clean_minify' ) ) {
	rocket_clean_minify();
}
```

This code needs to be saved in a file in the user’s root WordPress folder ( where wp-config.php and wp-load.php are located). It can be named anything, but for the sake of simplicity to the user, we’ll name it rocket-clean-domain.php. I’ve uploaded the file here so you can send it directly to the user without having them create it themselves. 

### The Cron job

We've taken care of the code, and now we need to explain to the user how to set up a Cron job.

There are two main ways to do this, the main difference being that one is a “real” Cron job, and the other uses a function that’s built into WordPress called WP-Cron. WP Cron has its limitations due to not being a “real” cron job, so we’ll need the user to create a “real” Cron job on their server. 

To create real cron jobs, we will first need to stop WP Cron from running. This is also great for the user's site as it won't run anytime someone visits the site. 

To do this, the user needs to add code to their wp-config.php file. They can do this by connecting to their server via FTP and finding the file, or if they’re using CPanel, they can use the File Manager to edit the file. 

Make sure you let them know to download a backup of the file before editing it since this is a critical file and it can break the site if anything goes wrong.

Once they’ve opened the file, they need to add the following line right above the line that says “/* That's all, stop editing! Happy publishing. */”:

```
define('DISABLE_WP_CRON', true);
```

![alt text](https://raw.githubusercontent.com/luanluta/WP-Media-Technical-Assessment/main/Disable%20Cron.png "Disable Cron")

Since we’ve disabled WP Cron we now need to create a real cron job that runs it so that the site’s automatic tasks still get run. They need to add this  to their cron job:

```
wget -q -O - http://yourdomain.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1
```

Make sure to replace https://yourdomain.com with the actual URL of the website. For example, if the website is https://basketballfanatic.com, then the proper command would be:

```
wget -q -O - https://basketballfanatic.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1
```

Next, we will need to add a cron job for running the rocket-clean-domain.php file we created in the previous step. The command for that would be:

```
/path/to/wp/installation/root/rocket-clean-comain.php
```

**Make sure to replace /path/to/wp/installation/root with the actual path of the WordPress root folder.**


Each server has its way of doing this, so they may require assistance from their hosting provider, however, if they’re using CPanel, we have a video that shows them how to do it here: 

* [For the WP Cron cron job ( I would suggest they run it once per 5-10 minutes )](https://recordit.co/A4Jj1Kg7x9)
* [For the rocket-clean-comain.php cron job](https://recordit.co/cl2YCoMCzu)

Please let me know if anything is unclear, or if the user is having any issues and I'll be happy to assist.


---


## Problem B

**Note: Since I don’t have specifics on the issue, I’m not able to provide a concrete solution here, although I will mention some common issues and I will run through my “workflow” of troubleshooting issues with Cron jobs not running.**

When setting up a Cron job to run PHP code that does something, the issue can be in one of two places (which is why we divided the main task into two smaller ones previously):

* The code doesn’t do what we want it to do, that is, it’s incorrect.
* Issues with the actual Cron job, like not being set up properly, or issues with the server.

Since I’ve tested the code on my environment and it ran fine, the issue must be with how the Cron job was set up.

The three most common reasons the Cron job didn’t work would be:

* Misconfigured Cron job
* Permissions issues
* Server unavailability

## Misconfigured Cron job:

The two main things I’d check here are if the schedule is what the user expects and if the cron commands are correct. 

Scheduling issues can mean that the Cron job is working as expected, but the time when it was supposed to run wasn’t set up properly, hence, we expect that it should’ve done its task and think that there is an issue. Clarifying with the user when exactly they expect the Cron job to run and check this against the actual settings usually solves this issue.

The Cron commands may not be correct, that is, the file path to the rocket-clean-domain.php file in this case may not be entered correctly. This can be checked by getting the correct path to the file and checking it against what was entered in the command.

## Permission issues:

The user account that created the Cron job doesn’t have permission to access the file we’re pointing to. To run properly, a cron job must be run under a user account that has the appropriate permissions to access the necessary files and resources. A fix for this would be to usually talk with any higher-level users or the hosting team to see if we can change the permissions and allow the Cron job to run.


## Server unavailability:

This is a common issue with cheaper shared hosting plans. The server or machine that the cron job is running on is not available or is experiencing technical issues. If the server is down or is not responding, the cron job will not be able to run. Unfortunately, there isn’t much we can do about this other than upgrading to better hosting. 

## My Workflow:

* Log into the server ( I will be assuming CPanel since it’s the most common one with WordPress installs)
* Check the PHP file for any errors. Although the code worked on my end, I can’t be sure that it was correctly copied.
* While there, check the path where it’s located. Although we advised the root folder, the file might’ve been created at a different location.
* Check this path against the file path in the Cron job itself.
* Check that the schedule is correct for what the user expects
* Check the logs for the Cron job (located in /var/log/ with the name cron) for any clues.
* Check permission issues. The file path can cause issues with permissions, so I’d check if it’s an absolute file path, [which is advised by CPanel here](https://support.cpanel.net/hc/en-us/articles/360053675953-How-to-troubleshoot-user-level-cron-issues). I would also check the permissions of the file and the ownership and cross reference that with the user.

I gather all the clues I got from this and provide a solution, or at the very least, provide the user with the next steps that need to be taken to diagnose the exact cause.


