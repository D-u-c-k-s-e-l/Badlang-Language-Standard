# Language Specs
This file contains the Language specification for

## `call <label>`
<details><summary></summary>

Jumps to a label that returns.

`label` is the [label](#labels) to jump to.

When the return occurs the line after the call is executed.

[Error `55`](#errors) occurs when `label` is invalid.
</details>

## `catch <into>`
<details><summary></summary>

When something goes wrong the [label](#labels) `into` is called
and `'ERRORLEVEL'` is set to the error code.

[Error `55`](#errors) occurs when `into` is invalid.
An [error](#errors) will occur if [`error`](#error-error_number-scope) is invoked with a scope that leads to this call.
</details>

## Comments (`;`)
<details><summary></summary>

A comment is an ignored line.

A comment occurs when a `;` is found at he start of a line.

I suggest that when you're commenting on the behavior
of your code you put your comment first and then the code.

[Error `3`](#errors) may occur.
</details>

## Double quotes (`"`)
<details><summary></summary>

A double quote lets an argument contain spaces.

If the argument starts with `"` then it continues until the next (unescaped) `"`.

Variables addressed with `%` do not need this.
</details>

## The Equals Operator (`=`)
<details><summary></summary>

The equals operator runs an instruction and passes the result to another instruction.

It is used `<instruction> = <calculation>` where  
`instruction` is run and can call `calculation` which returns a value.  
This value can then be used by the callie.

[Error `6`](#errors) will occur if the instruction does not end with an argument named `=`.
</details>

## `error <error_number> [scope]`
<details><summary></summary>

Causes an error to occur.

`error_number` is an error specified in [Errors](#errors).  
`scope` is the scope to raise the error in (see [Scope](#scope)).
This is the way to get [return](#return-value-scope) values.

An [error](#errors) will occur.
</details>

## Escapement (`|`)
<details><summary></summary>

Prefixing a special character with a pipe (`|`) will make it behave like raw text.  
Special characters are `{`, `"`, `@`, `'`, `|`, `%`, and `$`.

Prefixing the following characters results in the following behavior.  
* `|n`  
	repression of a newline
* `|t`  
	repression of a tabulation
* `|xnn`  
	text character 0x`nn`
* `|unnnn`
	unicode character 0x`nnnn`
</details>

## `for <variable_name> <end> [<start> <step>] <=>`
<details><summary></summary>

Iterate a variable.

1) Creates a variable named `variable_name`.
2) Sets it to `start` (defaults to `0`).
3) Increments the variable by `step` (defaults to `1`).
4) Runs `=`.
5) Repeats steps 3-5 until the variable equals `end`.
</details>

## `goto <label>`
<details><summary></summary>

Jumps to a label.

`label` is the label to jump to.
</details>

## `if <expression> <=>`
<details><summary></summary>

Run a command if an expression is true.

`expression` is a number to check weather to run `=`.
when `expression` is `0` nothing happens
when expression is a number and not `0` or `NaN`, `=` is evaluated
when expression is not a number error `E20` occurs.
</details>

## `inclusion <lib_name> <=>`
<details><summary></summary>

Includes a "library".

[`=`](#the-equals-operator) is a json object full of lists.  
These lists contain instructions.

`lib_name` is a name given to the "library".

To access the commands in the library use `lib.command` where lib is the `lib_name`  
and command is the json key of the instruction you want to run.
</details>

## `jset <location> [file] <=>`
<details><summary></summary>

Sets a value in `file`.

`location` is where to set in `file`
addressed by slashes (`/`) from the root of the file  
or alternatively from the current location if it begins with "`./`".

[`=`](#the-equals-operator) is what to set this value to.

`file` is a path to a valid json file and defaults to the current file.
</details>

## `jval <location> [file]`
<details><summary></summary>

Returns the value found within `file`.

`location` is where to look in `file`
addressed by slashes (`/`) from the root of the file  
or alternatively from the current location if it begins with "`./`"

`file` is a path to a valid json file and defaults to the current file.
</details>

## Labels (`:`)
<details><summary></summary>

Labels are lines of code that can be jumped to.

A label can have almost any name but a few are
reserved and some restrictions apply.
* `:e` is reserved for the entrypoint.
* A label may not contain the characters "`:`", "` `", "`@`", "`$`", "`%`" and "`'`".

A label is referred to by either `:name` or `:'name'`.

Labels always reference the shortest available line unless quoted, for example:  
Say we have two labels, `:sup` and `:supper`.  
`:supper` will reference the line `:sup` by default.  
To change this we must add quotes to the reference (`:'supper'`).  
`:'supper'` now references the line `:supper`.  

Labels are case-insensitive. This is unaffected by the above method.

Labels may have arguments.  

Arguments follow the label name and assign values if passed.
Example: `:example_label fish` where a value for `fish` can be  
passed to `:example_label` (eg. `call :example_label 1`).
</details>

## The Math Operator (`{}`)
<details><summary></summary>

Anything within curly braces is executed as math.

Variables are converted to numbers.  
anything that is not a number is made into the awful `NaN`.  
Truthy means that a variable is greater than `0` and is not `NaN`.  
`NaN` operated with anything else results in `NaN`.

Everything is represented internally as a `double`.  
Here are the math operations: (Note: `a` and `b` are values)
* `a-b`: Subtraction  
	subtract `b` from `a`
* `a+b`: Addition  
	add `a` to `b`
* `a*b`: Multiplication  
	multiply `a` and `b`
* `-a`: Negation  
	negate `a`
* `a^b`: Exploration  
	`a` to the power of `b`
* `!a`: Inversion  
	`0` if `a` is truthy  
	`1` otherwise
* `a>b`: Gtr  
	`1` if `a` is greater than `b`  
	`0` otherwise
* `a=b`: Eq  
	`1` if `a` is equals `b`  
	`0` otherwise
* `a<b`: Lss  
	`1` if `a` is less than `b`  
	`0` otherwise
* `a<=b`: Leq  
	`1` if `a` is less than or equal to than `b`  
	`0` otherwise
* `a>=b`: Geq  
	`1` if `a` is greater than  or equal to`b`  
	`0` otherwise
* `a/b`: Division  
	`a` divided by `b`
* `a%b`: Modulation  
	remainder of `a` divided by `b`
* `a&b`: And  
	`1` if `a` and `b` are truthy  
	`0` otherwise
* `a|b`: Or  
	`1` if `a` or `b` is truthy  
	`0` otherwise
* `a~b`: Xor  
	`1` if `a` or `b` are truthy but `a` and `b` are not  
	`0` otherwise
* `a?b:c`: With  
	`c` if `a` is truthy  
	`b` otherwise
* `#a`: Truth  
	`1` if `a` is truthy  
	`0` otherwise
 
Order of Operations:
1) Exploration  
	Left to Right
2) Multiplication, Division, Modulation  
	Left to Right
3) Addition, Subtraction, Negation  
	Left to Right
4) Eq, Gtr, Lss, Geq, Leq  
	Right to Left
5) Truth, And, Or, Xor, Not  
	Outside to Inside then Right to Left
6) With  
	Left to Right
Parentheses override this order.
</details>

## `msg <message>`
<details><summary></summary>

Writes out a message.

`message` is the message to write out.
</details>

## `numerise <text>`
<details><summary></summary>

Returns the `text` converted into hex values and prefixed is with "`0x`".
</details>

## `random <min> <max> [is_int]`
<details><summary></summary>

Generate a random number.

`min` is the minimum number to generate (inclusive).
`max` is the maximum number to generate (inclusive).
`is_int` is weather to generate only integers (defaults to `1`).
</details>

## `return [value] [scope]`
<details><summary></summary>

Returns from a script or a label.

`value` is the value to return to the callie.
`scope` is the [Scope](#scope) to return to.
</details>

## `run <instructions> [args]`
<details><summary></summary>

Evaluates `instructions` with `args`.

`instructions` is a json style list.  
`args` is passed into the entrypoint.
</details>

## Scope
<details><summary></summary>

Scope is a number specifying how far out information lies.

Scope is created when: a [`run`](#run-instructions-args), or [`call`](#call-label) is made.
Scope is removed when [`return`](#return-value-exit) is called.

`0` is information used by the current script.  
`1` refers to information from the script's caller.  
`2` refers to information from the caller's caller.  
`3` and so on continue referring to callers that keep getting farther away.  
If there is no farther caller then it refers to the furthest caller.

`-1` refers to the farthest caller.  
`-2` refers to the 2nd farthest caller.  
`-3` and so on behave similarly.  
If there is no more callers then it refers to the current script.

"`NaN`" causes [error `20`](#errors).
</details>

## `split <text> <by> <index>`
<details><summary></summary>

Splits some text and gets a section.

`text` is split by `by`.  
`index` is which split section to select (starting with `0`).

The entrypoint can use arguments to act as a function.
</details>

## `sub <text> [start]:[end]`
<details><summary></summary>

Get the substring of some text.

`text` is the text to evaluate.  
`start` is the index to start from  
(defaults to `0` which is the first character).  
`end` is the inclusive index to end at   
(defaults to `-1` which is the last character).
</details>

## `var <scope> <varname> [=]`
<details><summary></summary>

Sets a variable to a value.

`scope` is the scope of the variable and may be "`l`" (local), "`p`" (player), or "`g`" (global).  
`varname` is the name of a variable.  
`=` is the result of a calculation and defaults to "`0`".
</details>

## `varexist <varname>`
<details><summary></summary>

Returns `1` if the variable `varname` exists.
Returns `0` otherwise.
</details>

## Variables (`$`, `@`, `%`)
<details><summary></summary>

Variables are editable information stored under a name.

* Prefixes:  
	A variable is referred to by either `'name'`, `$'name'`, `@'name'`, or `%'name'`.  
	Variables names prefixed by `@` get the value of said variable and paste it there to be evaluated. these are the most unsafe references.  
	Variables names prefixed by `$` get the value of said variable and paste it there after the `@` is pasted.
	This allows variables to name other variables.  
	Variables names prefixed by `%` are evaluated last. They paste the value but do not run it as code and confines the value to the current argument.  
	Variables names prefixed by nothing are used for instructions like `set`.

* Names:  
	A variable can have almost any name but may not contain special characters.  
	Variables are case-sensitive.

* [Scope](#scope):  
	Scope can be specified by postfixing the reference with`!`.  
	This is done `'variable_name'![1]`.  
	Notice that the scope is put within brackets (`[]`) and that the `!` is outside the quotes.  
	"`'variable_name'`" can be replaced with any reference to a variable.  
	"`22`" can be replaced by any scope number.

* Missing:  
	A variable might be referenced when it doesn't exist.

	If this happens [error `1`]()
</details>

## `while <expression> <=>`
<details><summary></summary>

Run a command while an expression is true.

`expression` is evaluated repeatedly to check weather to run `=`.
</details>

## Errors
<details><summary></summary>

Errors occur when *you* make a mistake!  
And of course you will!

So this is what the errors mean:
* `ERROR 1: VARY VARY INTERESTING... line xxx in script`
	<!-- Yes, that's a reference. -->
	Variable does not exist.  
	This is checked with [`varexist`](#varexist-varname)
* `ERROR 2: CAN YOU EVEN TYPE?? I SEE PROBLEMS WITH LINE xxx IN YOUR BROKEN script!!!`  
	Argument is of wrong type
	(eg. the word "fish" is not a number).
* `ERROR 3: NO YOU'RE NOT! (line xxx in script)`  
	Variable names containing any capitalization of
	"`groot`" are invalid.
	The same is true for comments.

	I apologize in advance if this is actually your name.
* `ERROR 4: Something isn't adding up. Please check line xxx in script.`  
	IDK, thought it'd be funny.  
	Anyways, your math is wrong.
* `ERROR 6: xxx, script`
	[The Equals Operator](#the-equals-operator) is not valid for this instruction.
* `ARROR 7: LOL, YOU CAN'T SPELL! (line xxx in script)`  
	You mistyped the instruction.
* `https://esolangs.org/wiki/Argh!  ...oops, wrong spelling, I meant arg.`
	Wrong number of arguments.
* `ERROR 12: CLOSING TIME! (line xxx in script)`  
	Quotes (`""`), Quotes (`''`), or Curly braces (`{}`) need to be closed.
* `ERROR 10: YOU CAN'T WALK HERE! (line xxx in script)`  
	Invalid path.
* `ERROR 20: NaNny, HeLp! I haVe A pRobLEm oN lIne xxx iN script!`  
	`NaN` is not a number!  
	It even stands for that!!
* `ERROR 55: [Helpful error message]: line xxx in script`  
	Missing label name name.

The `xxx` is the line number starting from `0`.  
`script` is the script location in `behaviors.json` addressed by slashes (`/`) and surrounded in quotes (`''`).

There is no error for a missing variable because referencing one will cause it to be created and set to "`0`".