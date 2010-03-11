# KohanaPHP5 and OOP

First, you should have a basic understanding of PHP and Object Oriented Programming.

Here are some various PHP5 and OOP questions, if you are unsure of any of the answers then you should read some of the links after the question, and consider reading some of the "Suggested Readings".

*  In a class, what is the difference between `public`, `private` and `protected`?  [php.net][php.visibility]

*  List as many "magic methods" as you can. [php.net][php.magic]

*  Describe what an `abstract` class is, and what an `abstract` method is. [php.net][php.abstract]

*  Describe what a `final` class is, and what a `final` method is. [php.net][php.final]

*  Describe what a `static` class variable is, and an example of when it's useful. [php.net][php.static]

*  Describe interfaces. [php.net][php.interfaces], [power (pdf) p. 73][power]

*  Explain the singleton pattern, why it's useful, and what the `Bar::instance()` (sometimes called `Bar::getInstance()` method is for. [power (pdf) p. 97][power]

*  Explain the factory pattern, why it's useful, and the difference between `$foo = new Bar` and `$foo = Bar::factory()` [power (pdf) p. 98][power]

*  Explain the strategy pattern and the observer pattern. [power (pdf) p. 95, 101][power]

*  Explain Reflection [power (pdf) p. 105][power]

*  What is the difference between `$this` and `self::`?  Also, what is `parent::`? [power (pdf) p. 70][power]

*  Explain what a superglobal is, and give an example of one. [php.net][php.superglobals]

*  What is the difference between single quotes and double quotes on a string?  When should they be used? [power (pdf) p. 20][power], [php.net][php.strings]

*  What is a HEREDOC and a NOWDOC, when are they useful, and what php version is required to use a NOWDOC? [php.net][php.strings]

*  What is the difference between `if ($foo == false)` and `if ($foo === false)`? [php.net][php.comparisons], [php.net 2][php.comparisontable]

*  What is the silence operator? And why should it be avoided? [power (pdf) p. 39][power], [php.net][php.silence]

*  What is the ternary operator? [power (pdf) p. 39][power], [php.net][php.ternary]

*  What is XSS (cross-site scripting), and how can it be prevented? 

*  What is SQL-injection, and how can it be prevented?

## Recommended Reading

[PHP 5 Power Programming (pdf)][power] - The whole book is great, but I recommend the first 5 chapters, especially if you were unsure about any of the questions above.

[power]: http://ptgmedia.pearsoncmg.com/images/013147149X/downloads/013147149X_book.pdf "PHP Power Programming (pdf)"
[php.visibility]: http://php.net/manual/en/language.oop5.visibility.php
[php.magic]: http://php.net/manual/en/language.oop5.magic.php
[php.abstract]: http://php.net/manual/en/language.oop5.abstract.php
[php.final]: http://php.net/manual/en/language.oop5.final.php
[php.interfaces]: http://php.net/manual/en/language.oop5.interfaces.php
[php.static]: http://php.net/manual/en/language.oop5.static.php
[php.superglobals]: http://php.net/manual/en/language.variables.superglobals.php
[php.strings]: http://php.net/manual/en/language.types.string.php
[php.comparisons]: http://php.net/manual/en/language.operators.comparison.php
[php.comparisontable]: http://www.php.net/manual/en/types.comparisons.php
[php.silence]: http://www.php.net/manual/en/language.operators.errorcontrol.php
[php.ternary]: http://php.net/manual/en/language.operators.comparison.php#language.operators.comparison.ternary