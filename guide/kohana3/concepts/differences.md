# Differences Between Kohana 2 and Kohana 3

This is not meant to be a "how to upgrade" or even a list of "gotchas" you should now about when upgrading.  It's meant to be an **objective look at the differences between the two versions**, specifically between 2.4 and 3.0.

Kohana 2.4 is the continuation of the 2.x branch, which was originally based on Code Ignitor.  Kohana 3.x is *essentially* a rewrite of the framework.  However, they still have **a lot** of overlap &mdash; not just the fact that they both have the same goals of being a lightweight, secure, flexible PHP 5 frameworks &mdash; including the I18n, Configuration, Logging, Database and ORM.  Kohana 3 modules can easily be used in 2.4 with little to no modification. \[TODO: source? example? ]

Kohana 2.4 and 3.x are fairly similar, however there are a couple differences, which I will attempt to outline here.

[!!] Some of this article is borrowed from **about.upgrading** in the Kohana 3 userguide.

## File Structure / Naming Conventions

The file structure is one of the biggest changes in Kohana 3.  The Filestructure was completely revamped to allow better autoloading and namespacing.

### Kohana 2.x

In 2.x the "type" of file was typically a suffix, such as `User_Controller` or `User_Model`, and each type of class had its own folder, i.e `application/controllers` and `application/models` and `application/libraries`.  Classes in subfolders (other than controllers) had to be included with a call to Kohana::find_file() prior to use. 

#### Kohana 2.x sample file structure

~~~
controllers/
    foo.php               Class Foo_Controller Extends Controller
    admin.php             Class Admin_Controller Extends Controller
    admin/
        users.php         Class Admin_Users_Controller Extends Controller

models/
    bar.php               Class Bar_Model extends Model
    grault.php            Class Grault_Model extends ORM
    grault_garply.php     Class Grault_Garply_Model extends Grault_Model

helpers/
    fred.php              Class Fred

libraries/
    foobaz.php            Class Foobaz
    foobaz/
        plugh.php         Class Foobaz_Plugh extends Foobaz (needs to be manually included before use)

hooks/
    foo.php               (binds functions to events)
~~~

**Controllers** are the heart of your application.  Each request (typically) corresponds to a function in a controller. Each controller should be named `Name_Controller` and should `extend Controller` or another controller.

**Models** are how you interact with your data. Typically each model represents a table, and each object of that model represents one row.  Each model should be named `Name_Model` and should `extend Model` or some kind of modeling system like ORM.

**Helpers** are "simply handy functions that help you with development" and where typically static classes that did simple things. Examples: HTML, Text and Inflector.

**Libaries** were typically classes that you could instanciate and use for anything that a helper couldn't do.  Examples: Database, ORM, Image and Validation.

**Hooks** were files that would bind functions to events, allowing you more flexibility and control over the execution of the app.

### Kohana 3.x

In 3.x this approach has been scrapped in favour of the Zend framework's filesystem conventions.  Essentially, **the name of each class is the path to the class itself, seperated by underscores instead of slashes**, and all classes are in the `classes` folder. (i.e. `Class Some_Class_File` is located at `classes/some/class/file.php`)  This allows the autoloader to load any file, no matter what it is (assuming it's in the correct location).  Controllers are put in `classes/controllers` and models are put in `classes/models`. All other classes are put in the `classes` folder or any subfolder.

\[TODO: mention package namespaces [link](http://forum.kohanaphp.com/comments.php?DiscussionID=2762&page=3#Comment_19821) ]

#### Kohana 3.x sample file structure
~~~
classes/
    controller/
        foo.php           Class Controller_Foo extends Controller
        admin.php         Class Controller_Admin extends Controller
        admin/
            users.php     Class Controller_Admin_Users extends Controller_Admin

    model/
        bar.php           Class Model_Bar extends Model
        grault.php        Class Model_Grault extends ORM
        grault/
            garply.php    Class Model_Grault_Garply extends Model_Grault

    foobaz.php            Class Foobaz
    foobaz/
        field.php         Class Foobaz_Field
        field/           
            thud.php      Class Foobaz_Field_Thud extends Foobaz_Field

bootstrap.php (application) or init.php (modules)
~~~

**Controllers** are the heart of your application.  Each request (typically) corresponds to a function in a controller. Each controller should be named `Controller_Name` and should `extend Controller` or another controller.

**Models** are how you interact with your data. Typically each model represents a table, and each object of that model represents one row.  Each model should be named `Model_Name` and should `extend Model` or some kind of modeling system like ORM.

**Classes** (that aren't controllers or models) are simply placed in the classes falder, or any subfolder.  Essentially **helpers** and **libraries** have been merged into this folder, since the distinction was not always clear.  These files don't have to extend anything, but typically should if you want transparent extension to work, or have a field system like Sprig.

**bootstrap.php** is the file that setups Kohana, includes modules and launches the main request.

**init.php** (essentially the bootstrap for modules) allows modules to include routes or anything else they need to function.

### Summary

In 3.x **the name of each class is the path to the class itself, seperated by underscores instead of slashes**, and the type (controller,model) is at the beginning, rather than the end.  (`Controller_Foo` vs `Foo_Controller`)

Helpers and Libaries have been merged into the `classes` folder.

Hooks and Events have been removed, see "Bootstrap vs Events" below. 

## Bootstrap vs Events

Events were removed in 3.x for two reasons. First, events are not very HMVC friendly. (Which request triggers the event? How do you differentiate?)  Second, they are no longer necessary as the bootstrap is in the `application/` directory, and modules have an `init.php` (essentially a bootstrap for modules).  Other events such as `post_controller_constructor` have been replaced with `before()` and `after()` in the controllers.  If you want to add events, you can do it yourself, or use one of the many modules available (search github or the forums).
[Source](http://dev.kohanaframework.org/issues/2693#change-8006) 
[Source](http://forum.kohanaphp.com/comments.php?DiscussionID=2762&page=1#Item_18)
[Source](http://forum.kohanaphp.com/comments.php?DiscussionID=2762&page=3#Item_13)

## Controllers

### Kohana 2.x

In 2.x the each controller had the suffix `_Controller`, and every function was callable, unless it had an prefixed underscore, making it private.

#### Sample Kohana 2.x Controller
~~~
Class Foo_Controller extends Controller {
	
	public function index() {
		...
	}
	
	public function add() {
		...
	}
	
	public function _private_function() {
		// this function can not be reached by calling "foo/_private_function"
	}
}
~~~

### Kohana 3.x

In 3.x the class has the prefix `Controller_` (as per the new filesystem conventions), and each callable action must have the prefix `action_`. Private functions do not have any prefix.

#### Sample Kohana 3.x Controller
~~~
Class Controller_Foo extends Controller {
	
	public function action_index() {
		...
	}
	
	public function action_add() {
		...
	}
	
	public function private_function() {
		// this function can not be reached by calling "foo/private_function"
	}
}
~~~
### Summary

Controller names were changed to match the new filesystem conventions.

The `action_` prefix was added so each callable function much be explicitly declared.  This was to prevent accidentally forgetting to make a utility function private using the underscore prefix. 
[Source](http://forum.kohanaphp.com/comments.php?DiscussionID=2762&page=2#Comment_19797)

## Views

There have been a few minor changes to the View library which are worth noting.

In 2.3 views were rendered within the cope of the controller, allowing you to use `$this` as a reference to the controller within the view, this has been changed in 3.x. Views now render in an empty scope. If you need to use `$this` in your view you can bind a reference to it using [View::bind]: `$view->bind('this', $this)`.

It's worth noting, though, that this is *very* bad practice as it couples your view to the controller, preventing reuse.  The recommended way is to pass the required variables to the view like so:

~~~
$view = View::factory('my/view');

$view->variable = $this->property;

// OR if you want to chain this

$view
	->set('variable', $this->property)
	->set('another_variable', 42);
	
// NOT Recommended
$view->bind('this', $this);
~~~

Because the view is rendered in an empty scope `Controller::_kohana_load_view` is now redundant.  If you need to modify the view before it's rendered (i.e. to add a generate a site-wide menu) you can use [Controller::after].

~~~
<?php

Class Controller_Hello extends Controller_Template
{
	function after()
	{
		$this->template->menu = '...';
		
		return parent::after();
	}
}
~~~

## Config files

Another subtle difference the way config files are written.  Rather than setting keys in a variable called `$config` like 2.x, in 3.x the config file simply returns an array.

#### Kohana 2.x

~~~
$config['default'] = array
(
	'benchmark'     => FALSE,
	'persistent'    => FALSE,
	... [trimmed]
~~~

#### Kohana 3.x

~~~
return array
(
	'default' => array
	(
		'type'       => 'mysql',
		'connection' => array(
			... [trimmed]
~~~

## Input Library

The Input Library has been removed from 3.x in favour of just using `$_GET` and `$_POST`.  This means you are responsible for sanitizing input, as it is no longer done automatically.

### XSS Protection

If you need to XSS clean some user input you can use [Security::xss_clean] to sanitise it, like so:

	$_POST['description'] = security::xss_clean($_POST['description']);

You can also use the [Security::xss_clean] as a filter with the [Validate] library:

	$validation = new Validate($_POST);
	
	$validate->filter('description', 'Security::xss_clean');

### POST & GET

One of the great features of the Input library was that if you tried to access the value in one of the superglobal arrays and it didn't exist the Input library would return a default value that you could specify i.e.:

#### Kohana 2.x
	$_GET = array();
	
	// $id is assigned the value 1
	$id = Input::instance()->get('id', 1);
	
	$_GET['id'] = 25;
	
	// $id is assigned the value 25
	$id = Input::instance()->get('id', 1);

In 3.x you can duplicate this functionality using [Arr::get]:

#### Kohana 3.x
	$_GET = array();
	
	// $id is assigned the value 1
	$id = Arr::get($_GET, 'id', 1);
	
	$_GET['id'] = 42;
	
	// $id is assigned the value 42
	$id = Arr::get($_GET, 'id', 1);

## URL Helper

Some of the functionality that was previously provided by the url helper has been moved into the request library, and is available in controllers using the request object of the controller, typically `$this->request`.

`url::site()`, `url::base()` and `url::title` are the essentially the same.  (Although it's recommended that links use [reverse routing](kohana301.routing#reverse).)


  Kohana 2.x   |  Kohana 3.x
---------------|--------------
`url::redirect()` | `$this->request->redirect()` (in controllers) or `Request::instance()->redirect()`
`url::current()` | `$this->request->uri()` (in controllers) or `Request::instance()->uri()`
`uri::segment()` | `$this->request->param()` (in controllers) or `Request::instance()->param()`

## ORM

Please see the ORM section in the Kohana 3 Userguide "Upgrading" page.