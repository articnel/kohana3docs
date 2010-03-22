# Kohana 3 Bootstrap

The bootstrap (located at `application/bootstrap.php`) is probably the most important file in your Kohana 3 application.  But, before we get to the bootstrap the index.php file is called.  All requests are routed through index.php via a rewrite rule, index.php defines where the `application`, `modules`, and `system` folders are (and defines the constants `DOCROOT`, `APPPATH`, `MODPATH`, and `SYSPATH`) It then includes the Kohana Core, and finally includes the bootstrap.

## The Bootstrap

The bootstrap is responsible for the flow of your application.  In previous versions of Kohana the bootstrap was in `system` and was somewhat of an unseen, uneditible force.  In Kohana 3 the bootstrap takes on a much more integral and versatile role.  Do not be afraid to edit and change your bootstrap however you see fit.

### What it does

#### Evironment setup

First the bootstrap sets the timezone and the locale, adds Kohana's autoloader so the [cascading filesystem](kohana301.cascade) works.  You could add any other settings that all your requests needed here.  (Examples?)

~~~
// Sample excerpt from bootstrap.php with comments trimmed down

// Set the default time zone.
date_default_timezone_set('America/Chicago');
 
// Set the default locale.
setlocale(LC_ALL, 'en_US.utf-8');
 
// Enable the Kohana auto-loader.
spl_autoload_register(array('Kohana', 'auto_load'));
 
// Enable the Kohana auto-loader for unserialization.
ini_set('unserialize_callback_func', 'spl_autoload_call');
~~~

#### Configuration and Initilization

First the bootstrap sets the timezone and the locale, adds Kohana's autoloader so the [cascading filesystem](kohana301.cascade) works, then initilizes Kohana, including setting up the config reader and the log writer.

~~~
// Sample excerpt from bootstrap.php with comments trimmed down

Kohana::init(array('
    base_url' => '/kohana/',
	index_file => false,
));

// Attach the file writer to logging. Multiple writers are supported.
Kohana::$log->attach(new Kohana_Log_File(APPPATH.'logs'));

// Attach a file reader to config. Multiple readers are supported.
Kohana::$config->attach(new Kohana_Config_File);
~~~

An example of how you can modify your bootstrap to better suite your needs can be found in the [Kohanaphp.com source][github/kohanaphp.com].  We test whether the site is live by checking if the `$_SERVER['HTTP_HOST']` is 'kohanaphp.com', and if it is, set `Kohana::$environment` to 'live'.  When we initialize Kohana, we use the environment variable to set things like base_url, caching, and profile.  We also use it later to only show stack traces if we are in development. `Kohana::$environment` is a totally optional variable that you may use in cases like this.  Here are the relevant excerpts:

~~~
// Exceprt from http://github.com/isaiahdw/kohanaphp.com/blob/f2afe8e28b22b93527b2aa94032abda8447274e5/application/bootstrap.php
... [trimmed]
spl_autoload_register(array('Kohana', 'auto_load'));
 
/**
 * Set the environment status by the domain.
 */
if (strpos($_SERVER['HTTP_HOST'], 'kohanaphp.com') !== FALSE)
{
	// We are live!
	Kohana::$environment = 'live';
 
	// Turn off notices and strict errors
	error_reporting(E_ALL ^ E_NOTICE ^ E_STRICT);
}
 
/**
 * Initialize Kohana, setting the default options.
 ... [trimmed]
 */
Kohana::init(array(
	'base_url'   => Kohana::$environment === 'live' ? '/' : '/kohanaphp.com/',
	'caching'    => Kohana::$environment === 'live',
	'profile'    => Kohana::$environment !== 'live',
	'index_file' => FALSE,
));

... [trimmed]

try
{
	$request = Request::instance()->execute();
}
catch (Exception $e)
{
	// If we are in development and the error wasn't a 404, show the stack trace.
	if ( Kohana::$environment == "development" AND $e->getCode() != 404 )
	{
		throw $e;
	}
...[trimmed]
~~~



#### Modules

**Modules in Kohana are essentially self-contained extensions to the [cascading filesystem](kohana301.cascade).**

You load modules using `Kohana::modules()`.  Including modules is completely optional, in fact, the [Kohanaphp.com example][github/kohanaphp.com] I showed earlier doesn't even call `Kohana::modules()`.

Each key in the array needs to be the name of the module (this is used in the userguide, so be sure it's correct if you want the userguide to work correctly), and the value is the path to the module, either relative or absolute.
~~~
// Example excerpt from bootstrap.php

Kohana::modules(array(
	'database'   => MODPATH.'database',
	'orm'        => MODPATH.'orm',
	'userguide'  => MODPATH.'userguide',
));
~~~

#### Routes

Next we define the routes that are specific to our application.  I will not go into depth here, read the [Routing](kohana301.routing) page for more info.

~~~
// The default route that comes with Kohana 3
Route::set('default', '(<controller>(/<action>(/<id>)))')
	->defaults(array(
		'controller' => 'welcome',
		'action'     => 'index',
	));
~~~

#### Execution

After we have Initialized Kohana and our modules and defined our routes it's time to actually execute our application.  This is probabably the area of the bootstrap that is most flexible.  Do not be afraid to change this around to whatever works best for you. 

##### Basic Example
The most simple way to do this, and what comes default with Kohana 3 is simply:
~~~
// Execute the main request
echo Request::instance()
	->execute()
	->send_headers()
	->response;
~~~

##### Catching Exceptions

However, the previous example provides no error catching, which means stack traces would be shown which will probably give sensitive info (like database user/pass) if an error is shown, which is very bad.  We should add a `try catch` block.  If we get an exception, we will show an error view.  You should have a view located at `views/errors/404.php`.  **Note:** Because we catch the exception, Kohana will no longer log the error by default.  It is your responsibility to log the error.

~~~
$request = Request::instance();

try
{
	// Execute the main request
	$request->execute();
}
catch (Exception $e)
{
	// Be sure to log the error
	Kohana::$log->add(Kohana::ERROR, Kohana::exception_text($e));
	
	// If there was an error, send a 404 response and display an error
	$request->status   = 404;
	$request->response = View::factory('errors/404');
}

// Send the headers and echo the response
$request->send_headers();
echo $request->response;
~~~

##### Last thoughts

In production, **your application should never have any uncaught exceptions**, as this can expose sensitive information (via the stack trace).  In the previous example we make the assumption that there is actually a view called 'views/errors/404', which is fairly safe to assume.  One solution is to turn 'errors' off in Kohana::init for your production machine, so it displays the normal php errors rather than a stack trace.

~~~
// snippet from bootstrap.php 
Kohana::init(array('
    ...
    'errors' => false,
));
~~~

So rather than displaying the Kohana error page with the stack trace, it will display the default php error. Something like:

**Fatal error: Uncaught Kohana_View_Exception [ 0 ]: The requested view errors/404 could not be found ~ SYSPATH/classes/kohana/view.php [ 215 ] thrown in /var/www/kohanut/docs.kohanaphp.com/3.0/system/classes/kohana/view.php on line 215**

Keep in mind what I said earlier though: **your application should never have any uncaught exceptions**, so this should not be necesarry, though it is a good idea, simply because stack traces on a production environment are a *very* bad idea.

Another solution is to always have a `catch` statement that can't fail, something like an `echo` and an `exit` or a `die()`.  This should almost never be necesarry, but it makes some people feel better at night.  You can either wrap your entire bootstrap in a try catch, or simply wrap the contents of the catch in another try catch.  For example:

~~~
try
{
	// Execute the main request
	$request->execute();
}
catch (Exception $e)
{
	try
	{
		// Be sure to log the error
		Kohana::$log->add(Kohana::ERROR, Kohana::exception_text($e));
		
		// If there was an error, send a 404 response and display an error
		$request->status   = 404;
		$request->response = View::factory('errors/404');
	}
	catch
	{
		// This is completely overkill, but helps some people sleep at night
		echo "Something went terribly wrong. Try again in a few minutes.";
		exit;
	}
}
~~~

[github/kohanaphp.com]: http://github.com/isaiahdw/kohanaphp.com/blob/f2afe8e28b22b93527b2aa94032abda8447274e5/application/bootstrap.php