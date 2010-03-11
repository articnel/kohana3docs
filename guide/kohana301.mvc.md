# Model View Controller

Model View Controller is a architectural pattern used in programming that helps to seperate presentation (view) from application logic (controller) and business logic (model).

Almost all HTML should be in your views, almost all interaction with the database should be in your models, and the application logic is in your controllers.

\[TODO: needs more substance here]

## Paradigms

#### Fat Models, Skinny Controllers

This is a saying that many MVC and Kohana users say.  In essence it means your models should be fat, and your controllers should be skinny.  Your controller methods should typically be fairly short, no more than about 20 source lines of code.  This is not universally true, but often if your controller methods are really long, you are probably Doing It Wrong.  Your controller methods should usually just call some function(s) in a model, do something if there was an error, or do something else if it worked.

## Examples of "Doing It Wrong"

\[TODO: need some examples]