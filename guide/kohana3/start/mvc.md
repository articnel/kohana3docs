# Model View Controller

Hopefully you have some experience with MVC.  If not, this will attempt to introduce you to it.

Model View Controller is a architectural pattern used in programming that helps to seperate presentation (view) from application logic (controller) and business logic (model).

**Views** should contain all of your markup.  Your controller's and model's should not typically contain any `echo` statements or any HTML markup.  An exception to this would be debugging your code.



Almost all HTML should be in your views, almost all interaction with the database should be in your models, and the application logic is in your controllers.

\[TODO: needs more substance here]

## Paradigms

#### Fat Models, Skinny Controllers

This is a saying that many MVC and Kohana users say.  In essence it means your models should be fat, and your controllers should be skinny.  Your controller methods should typically be fairly short, no more than about 20 source lines of code.  This is not universally true, but often if your controller methods are really long, you are probably Doing It Wrong.  Your controller methods should usually just call some function(s) in a model, do something if there was an error, or do something else if it worked.

## Examples of "Doing It Wrong"

~~~
public function action_index()
{
	// Bad! This should be in the view
	echo '<h1>Welcome to our site!</h1>';
	
	// Bad! Don't have html in your controller
	$error = '<div class="error">Not found</div>';
	
	// Good. Just pass the error to the view, and let it render it.
	$this->request->response = View::factory('foobar',array('error'='Not found'));
	
	// Only use temporarily for debugging
	echo Kohana::debug($foo);
~~~