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

This reqeust can be split up into two smaller tasks:

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

To create real cron jobs, we will first need to stop WP Cron from running. This is also great for the users site as it wont run anytime smeone visits the site. 

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

Please let me know if anything is unclear, or the user is having any issues and I'll be happy to assist.

