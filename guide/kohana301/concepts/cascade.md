# Kohana 3 Cascading Filesystem

Understanding Kohana's Cascading Filesystem is vital to understanding Kohana.

Thanks to Kohana's autoloader, you do not have to `include` or `require` files when you need to use class, whether it be a model, a controller or a regular class like `Arr` or `Request`.  When you try to use a class that hasn't been included yet, `Kohana::find_file()` is called.  `Kohana::find_file()` is also called when you ask for a view, everytime you ask for a config, everytime something needs to be translated, etc.  Basically, all the time.  If you have profiling on you can see how many times it's called.

#### "Paths" and "Highest"

Throughout this page I will use the terms "paths", and "higher" or "highest".  When Kohana looks for files it looks in several places.  First it looks in application, then in each modules, then finally in the system folder, in that order.  The term "paths" refers to those folders.  The terms "higher" or "highest" refer to which of those folders takes precedence.  The application folder is the "highest", then each module **in the order they are listed** when you call `Kohana::modules()`, then the system folder.

![Kohana's cascading filesystem](img/cascade.png)

### What Kohana::find_file() does

When `Kohana::find_file()` is called Kohana searches all of the "paths" for that file.  Let's say we have two modules loaded, "database" and "userguide" (in that order) and we call `View::factory('something')`. The View class will call `Kohana::find_file('views','something')` which will look **in this order** for the file:

~~~
application/views/something.php
modules/database/views/something.php
modules/userguide/views/something.php
system/views/something.php
~~~

If it can't find it in any of those places, Kohana::find_file() will return false, and View::factory() will throw an exception.  If it is found, then it will load that view as expected.

One thing to think about is this:  If the file exists in more than one place, the 'highest' one is used, and the other(s) are *essentially ignored*.  For example, if `modules/userguide/views/something.php` and `application/views/something.php` both existed, the one in application is used, and the one in the userguide module is ignored.  This is the basis of **Transparent Extension**

### Transparent Extension

Let's say you want to add a function to a class declared in `system` or even in a module.  Because of the naming system that Kohana uses it's very easy.  Let's use the `Form` class as an example.  First, let's take a look at `system/classes/form.php`:

~~~
// system/classes/form.php
<?php defined('SYSPATH') or die('No direct script access.');
 
class Form extends Kohana_Form {}
~~~

Pretty boring, actually.  Most of the action happens in `system/classes/kohana/form.php`, but because of this empty file we can add a function very easily.  First copy `system/classes/form.php` to `application/classes/form.php` and then add or change whatever you want.  In this example I'll add `form::close()` back in.  (Even though it's a silly function and no one should use it.)

~~~
// application/classes/form.php
<?php defined('SYSPATH') or die('No direct script access.');
 
class Form extends Kohana_Form {
	public static function close()
	{
		echo '</form>';
	}
}
~~~

So now when we call or use the Form class, `Kohana::find_file()` will look for `classes/form.php`, which it finds in application (rather than in system), it then tries to find Kohana_Form, which it finds in system like usual.

You can also do this to functions that exist, to change or add functionality.  As an example, here is a [Request class](http://github.com/bluehawk/kohanut-core/blob/dbe6afc67ff03529461d4d1e88910a59ca0fa6cd/classes/request.php) that adds `__call()` back in.  Simply save this file in the `classes` directory of application or any module.

Because of this, **you should never, ever have to edit any files in system**.  If you need to make a change, just use transparent extension.

### Config, I18n and Messages are merged

It's important to note that config, i18n and messages are slightly different.  Normally, if more than one file with the same name exists, than whichever one is higher is used and the others are ignored.  The exception to this rule is config, i18n, and message files.  These files are merged together and they are all used.  Meaning if the file `application/config/database.php` and `modules/database/config/database.php` both exist (which is often the case), they are merged together and both are used.

As an example, let's say we want to add "deer", "moose" and "cactus" to the Inflector class.  The inflector uses config files for the list of words that don't follow the rules.  Our new config file should have a similar layout to the default [config/inflector.php](http://github.com/kohana/core/blob/8696209b3560d9fe7a9b898696b370f8e3702e89/config/inflector.php) that comes with Kohana. Here is our `config/inflector.php`: (Remember that this file can be placed in application/config or any module's config folder.)

~~~
<?php defined('SYSPATH') OR die('No direct access allowed.');

return array(

	'uncountable' => array(
		'deer',
		'moose',
	),

	'irregular' => array(
		'cactus' => 'cactii',
	),
);
~~~

We can now use `Inflector::plural('deer')` or `Inflector::plural('cactus')` and it will give us the correct word, but all the old words will still work, like `Inflector::plural('child')` because our file is **merged** with the default one, rather than replacing it. An easy way to understand this would be to `echo Kohana::debug(Kohana::config('inflector'))`.  You would see the result of merging our inflector.php with the default inflector.php.

**Note:** The arrays are merged, and if there are duplicate keys, then the file that is "higher" takes precedence. For example:

~~~
//application/config/inflector.php
return array(
	'irregular' => array(
		'child' => 'more than one child',
	)
)
~~~

If you called `Inflector('child')` it would return 'more than one child', rather than 'children' as defined in `system/config/inflector.php`.

### "The Squish"

Another way to look at the cascading file system is if you took each module (one at a time) and the application folder and moved it into the system folder, selecting "replace existing files" when prompted. (Please don't do this, its merely an example.)  The resulting "squished" or merged file structure is one way to look at it.

\[TODO: Include a newer version of Geert's cascading image from the old docs]