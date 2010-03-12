# Differences Between Kohana 2 and Kohana 3

Kohana 3 is essentially a rewrite of Kohana 2, but they do share many of the same principles and ideas and even libraries.  In fact, probably 50-75% of the code is the same or very similar, but there are a couple of signifigant differences.

[!!] Much of this article is borrowed from **about.upgrading** in the Kohana 3 userguide.

## File Structure / Naming Conventions

In 2.x the "type" of file was typically a suffix, such as `User_Controller` or `User_Model`.  Using classes (other than controllers) in subfolders required a call to Kohana::find_file() and was less intuitive.

In 3.x this approach has been scrapped in favour of the Zend framework's filesystem conventions.  Essentially, the name of each class is the path to the class itself, seperated by underscores instead of slashes. (i.e. `/some/class/file.php` becomes `Some_Class_File`)  This allows the autoloader to load any file, no matter where it is.

All models, controllers and library classes are put in the `classes` folder.



Here are some [Metasyntactic](http://en.wikipedia.org/wiki/Metasyntactic_variable#Words_commonly_used_as_metasyntactic_variables) files just for fun:
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
        plugh.php         Class Foobaz_Plugh
        plugh/           
            thud.php      Class Foobaz_Plugh_Thud extends Foobaz_Plugh
~~~

Files in `classes/controller` should `extend Controller` or another controller.

Files in `classes/model/` should `extend Model`, another model, or some kind of model library, like ORM, Sprig, or Jelly.

Other files in `classes` don't have to extend anything, but can extend other classes if you want.  For example, if you want your module to work with transparent extension or for a field system, like in Sprig or Jelly.

## Bootstrap vs Events

\[TODO]

## Config files

Another subtle difference the way config files are written.  Rather than setting keys in a variable called `$config` like 2.x, in 3.x the config file simple returns an array.

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

The Input Library has been removed from 3.0 in favour of just using `$_GET` and `$_POST`. 

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

In 3.0 you can duplicate this functionality using [Arr::get]:

#### Kohana 3.x
	$_GET = array();
	
	// $id is assigned the value 1
	$id = Arr::get($_GET, 'id', 1);
	
	$_GET['id'] = 42;
	
	// $id is assigned the value 42
	$id = Arr::get($_GET, 'id', 1);

## ORM

Please see the ORM section in the Kohana 3 Userguide "Upgrading" page.

## URL Helper

Some of the functionality that was previously provided by the url helper has been moved into the request library, and is available in controllers using the request object of the controller, typically `$this->request`.

`url::site()`, `url::base()` and `url::title` are the essentially the same.  (Although it's recommended that links use [reverse routing](kohana301.routing#reverse).)


  Kohana 2.x   |  Kohana 3.x
---------------|--------------
`url::redirect()` | `$this->request->redirect()` (in controllers) or `Request::instance()->redirect()`
`url::current()` | `$this->request->uri()` (in controllers) or `Request::instance()->uri()`
`uri::segment()` | `$this->request->param()` (in controllers) or `Request::instance()->param()`

## View Library

There have been a few minor changes to the View library which are worth noting.

In 2.3 views were rendered within the cope of the controller, allowing you to use `$this` as a reference to the controller within the view, this has been changed in 3.0. Views now render in an empty scope. If you need to use `$this` in your view you can bind a reference to it using [View::bind]: `$view->bind('this', $this)`.

It's worth noting, though, that this is *very* bad practice as it couples your view to the controller, preventing reuse.  The recommended way is to pass the required variables to the view like so:

	$view = View::factory('my/view');
	
	$view->variable = $this->property;
	
	// OR if you want to chain this
	
	$view
		->set('variable', $this->property)
		->set('another_variable', 42);
		
	// NOT Recommended
	$view->bind('this', $this);

Because the view is rendered in an empty scope `Controller::_kohana_load_view` is now redundant.  If you need to modify the view before it's rendered (i.e. to add a generate a site-wide menu) you can use [Controller::after].

	<?php
	
	Class Controller_Hello extends Controller_Template
	{
		function after()
		{
			$this->template->menu = '...';
			
			return parent::after();
		}
	}