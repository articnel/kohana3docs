# Kohana Coding Convention

In order to produce highly consistent source code, we ask that everyone follow the coding standards as closely as possible.

[!!] Most of the article was taken from [here](http://dev.kohanaphp.com/wiki/kohana2/CodingStyle), some modifications were made to make it apply to Kohana 3.

## Brackets

Please use [BSD/Allman Style](http://en.wikipedia.org/wiki/Indent_style#BSD.2FAllman_style) bracketing.  This means that brackets are on their own line.

	<?php
	
	if ( $x == $y )
	{
		foo();
		bar();
	}

## Naming Conventions

Kohana uses under_score naming, not camelCase naming.

#### Classes

	<?php
	
	// Controller class, uses Controller_ prefix
	class Controller_Apple extends Controller {
	
	// Model class, uses Model_ prefix
	class Model_Cheese extends Model {
	
	class Database {
	
	class Database_Query {

When creating an instance of a class, don't use parentheses if you're not passing something on to the constructor:

	<?php
	
	// Correct:
	$db = new Database;

	// Incorrect:
	$db = new Database();
	
	// Correct:
	$db = new Database($foo);

#### Functions and Methods

Functions should be all lowercase, and use under_scores to seperate words:

	<?php
	
	function drink_beverage($beverage)
	{

#### Variables

All variables should be lowercase and use under_score, not camelCase:

	<?php
	
	// Correct:
	$foo = 'bar';
	$long_example = 'uses underscores';
	
	// Incorrect:
	$weDontWantThis = 'understood?';

## Identation

You must use tabs to indent your code. Using spaces for tabbing is strictly forbidden.

Vertical spacing (for multi-line) is done with spaces. Tabs are not good for vertical alignment because different people have different tab widths.

	<?php
	
	$text = 'this is a long text block that is wrapped. Normally, we aim for '
	      . 'wrapping at 80 chars. Vertical alignment is very important for '
	      . 'code readability. Remember that all indentation is done with tabs,'
	      . 'but vertical alignment should be completed with spaces, after '
	      . 'indenting with tabs.';

## String concatenation

Don't put spaces around the concatenation operator:

	<?php
	
	// Correct:
	$str = 'one'.$var.'two';
	
	// Incorrect:
	$str = 'one'. $var .'two';
	$str = 'one' . $var . 'two';

## Single Line Statements

Single-line `if` statements should only be used when breaking normal execution (e.g. `return` or `continue`):

	<?php
	
	// Acceptable:
	if ($foo == $bar)
		return $foo;
	
	if ($foo == $bar)
		continue;
	
	if ($foo == $bar)
		break;
	
	if ($foo == $bar)
		throw new Exception('You screwed up!');
	
	// Not acceptable:
	if ($baz == $bun)
		$baz = $bar + 2;

## Comparison Operations

Please use `OR` and `AND` for comparison:

	<?php
	
	// Correct:
	if (($foo AND $bar) OR ($b AND $c))
	
	// Incorrect:
	if (($foo && $bar) || ($b && $c))

## `if`/`else` Blocks

Please use `elseif`, not `else if`:

	<?php
	
	// Correct:
	elseif ($bar)
	
	// Incorrect:
	else if($bar)

## Switch structures

Each `case`, `break` and `default` should be on a separate line. The block inside a `case` or `default` must be indented by one tab.

	<?php
	
	switch ($var)
	{
		case 'bar':
		case 'foo':
			echo 'hello';
		break;
		case 1:
			echo 'one';
		break;
		default:
			echo 'bye';
		break;
	}

## Parentheses

There should be one space after statement name, followed by a parenthesis. The `!` (bang) character must have a space on either side to ensure maximum readability. Except in the case of a bang or type casting, there should be no whitespace after an opening parenthesis or before a closing parenthesis.

	<?php
	
	// Correct:
	if ($foo == $bar)
	if ( ! $foo)
	
	// Incorrect:
	if($foo == $bar)
	if(!$foo)
	if ((int) $foo)
	if ( $foo == $bar )
	if (! $foo)

## Ternaries

All ternary operations should follow a standard format. Use parentheses around expressions only, not around just variables.

	<?php
	
	$foo = ($bar == $foo) ? $foo : $bar;
	$foo = $bar ? $foo : $bar;

All comparisons and operations must be done inside of a parentheses group:

	<?php
	
	$foo = ($bar > 5) ? ($bar + $foo) : strlen($bar);

When separating complex ternaries (ternaries where the first part goes beyond ~80 chars) into multiple lines, spaces should be used to line up operators, which should be at the front of the successive lines:

	<?php
	
	$foo = ($bar == $foo)
	     ? $foo
	     : $bar;

## Type Casting

Type casting should be done with spaces on each side of the cast:

	<?php
	
	// Correct:
	$foo = (string) $bar;
	if ( (string) $bar)
	
	// Incorrect:
	$foo = (string)$bar;

When possible, please use type casting instead of ternary operations:

	<?php
	
	// Correct:
	$foo = (bool) $bar;
	
	// Incorrect:
	$foo = ($bar == TRUE) ? TRUE : FALSE;

When casting type to integer or boolean, use the short format:

	<?php
	
	// Correct:
	$foo = (int) $bar;
	$foo = (bool) $bar;
	
	// Incorrect:
	$foo = (integer) $bar;
	$foo = (boolean) $bar;

## Constants

Always use uppercase for constants:

	<?php
	
	// Correct:
	define('MY_CONSTANT', 'my_value');
	$a = TRUE;
	$b = NULL;
	
	// Incorrect:
	define('MyConstant', 'my_value');
	$a = True;
	$b = null;

Place constant comparisons at the end of tests:

	<?php
	
	// Correct:
	if ($foo !== FALSE)
	
	// Incorrect:
	if (FALSE !== $foo)

This is a slightly controversial choice, so I will explain the reasoning. If we were to write the previous example in plain English, the correct example would read:

	if variable $foo is not exactly FALSE

And the incorrect example would read:

	if FALSE is not exactly variable $foo

Since we are reading left to right, it simply doesn't make sense to put the constant first.

## Comments

####One-line comments

Use `//`, preferably above the line of code you're commenting on. Leave a space after it and start with a capital. Never use `#`.

	<?php
	
	// Correct
	
	//Incorrect
	// incorrect
	# Incorrect

## Regular expressions

When coding regular expressions please use PCRE rather than the POSIX flavor. PCRE is considered more powerful and faster.

	<?php
	
	// Correct:
	if (preg_match('/abc/i'), $str)
	
	// Incorrect:
	if (eregi('abc', $str))

Use single quotes around your regular expressions rather than double quotes. Single-quoted strings are more convenient because of their simplicity. Unlike double-quoted strings they don't support variable interpolation nor integrated backslash sequences like `\n` or `\t`, etc.

	<?php
	
	// Correct:
	preg_match('/abc/', $str);
	
	// Incorrect:
	preg_match("/abc/", $str);

When performing a regular expression search and replace, please use the `$n` notation for backreferences. This is preferred over `\\n`.

	<?php
	
	// Correct:
	preg_replace('/(\d+) dollar/', '$1 euro', $str);
	
	// Incorrect:
	preg_replace('/(\d+) dollar/', '\\1 euro', $str);

Finally, please note that the `$` character for matching the position at the end of the line allows for a following newline character. Use the `D` modifier to fix this if needed. [More info](http://blog.php-security.org/archives/76-Holes-in-most-preg_match-filters.html).

	<?php
	
	$str = "email@example.com\n";
	
	preg_match('/^.+@.+$/', $str);  // TRUE
	preg_match('/^.+@.+$/D', $str); // FALSE