
# Javascript Style Guide

Adapted from the jQuery style guide

## 1) Naming

```js
ClassNamesLikeThis
methodNamesLikeThis
variableNamesLikeThis
parameterNamesLikeThis
propertyNamesLikeThis
SYMBOLIC_CONSTANTS_LIKE_THIS
```

### Variables and properties referring to jQuery element objects are prefixed with $

example:

```js
/**
 * @param {string} selector The jQuery selector for elements to apply 
 *     fanciness to.
 */
function doSomethingFancy(selector) {
  var $elements = $(selector);
  // do something fancy with $elements ...
}
```

## 2) File names

```
file-names-like-this.js
template-names-like-this.handlebars
```

## 3) Whitespace and Code Formatting


Use 4-space indenting for all code. Do not use tabs.

`if/else/for/while/try` always have braces and always go on multiple lines, with the opening brace on the same line

Braces should always be used on blocks.

Bad:
```js
   if (true)
      blah();
```

Good:
```js
   if (true) {
      blah();
   }
```

`else/else if/catch` go on the same line as the brace.

```js
   if (blah) {
      baz();
   } else {
      baz2();
   }
```

Extra indentation should be used to clearly distinguish multiline conditionals from the following block of code (PEP8 addresses this for our Python code).

Bad:
```js
   if (someReallyLongBooleanVariableIMeanReallyLong &&
      someOtherBoolean) {
      return "monkeys";
   }
```

Good:
```js
   if (someReallyLongBooleanVariableIMeanReallyLong &&
         someOtherBoolean) {
      return "monkeys";
   }
```

## 4) Line length

Lines should not exceed 99 characters.
This is consistent with our Python style guide, which adheres to PEP8.

> For code maintained exclusively or primarily by a team that can reach agreement on this issue, it is okay to increase the nominal line length from 80 to 100 characters

## 5) Equality


Strict equality checks using `===` should be used in favor of `==` due to the numerous oddities related to JavaScript’s type coercion.

Examples where `==` can be confusing (all of the following statements evaluate to true):

```js
Boolean('0') == true
'0' != true
0 != null
0 == []
0 == false
Boolean(null) == false
null != true
null != false
Boolean(undefined) == false
undefined != true
undefined != false
Boolean([]) == true
[] != true
[] == false
[[]] == false
Boolean({}) == true
{} != true
{} != false
```

The only valid use of `==` is for comparing against null and undefined at the same time. 

```js
// Check null and undefined, but distinguish between other falsey values
if (someVariable == null) {
```

Though, in most cases, other falsey values should also be included and the above can be simplified to the preferred:

```js
if (!someVariable) {
```

## 6) Strings


Strings should use double quotes (“) instead of single quotes (‘)

## 7) Method and property visibility


Private methods and properties (in files, classes, and namespaces) should be named with a leading underscore.

While we do not currently use any compilers to enforce this, clients of an API or class are expected to respect these conventions.

example of some-module.js:

```js
function _PrivateClass() {
    // should not be instantiated outside of this file
}

function PublicClass(param) {
    this.publicMember = param;
    this._privateMember = new _PrivateClass();
}

var x = new _PrivateClass();  // OK - we’re in the same file.
var y = new PublicClass();  // OK
var z = y._privateMember;  // NOT OK!
```

Rationale: leading underscores for private methods and properties is consistent with the styles used in numerous JavaScript libraries, many of which we include in our code base (e.g. Backbone). It is also consistent with our Python style guide, lowering the mental effort for developers to switch between the two.

## 8) Array and Object literals

Always use `[]` and `{}` style literals to initialize arrays and objects, instead of the Array and Object constructor. 

Array constructors are error-prone due to their arguments.

Bad:
```js
// Length is 3.
var a1 = new Array(x1, x2, x3);

// Length is 2.
var a2 = new Array(x1, x2);

// If x1 is a number and it is a natural number the length will be x1.
// If x1 is a number but not a natural number this will throw an exception.
// Otherwise the array will have one element with x1 as its value.
var a3 = new Array(x1);

// Length is 0.
var a4 = new Array();
```

Because of this, if someone changes the code to pass 1 argument instead of 2 arguments, the array might not have the expected length.

To avoid these kinds of weird cases, always use the more readable array literal.

Good:
```js
var a = [x1, x2, x3];
var a2 = [x1, x2];
var a3 = [x1];
var a4 = [];
```

Object constructors don't have the same problems, but for readability and consistency object literals should be used.

## 9) Inline Comments.

Inline style comments should be of the // variety, not the /* */ variety.

## 10) Top level file and class comments

All files and classes should have JSDoc comments.

JSDoc can be parsed by a number of open source tools, and must be well-formed. 

Syntax:
```js
/**

 * A JSDoc comment should begin with a slash and 2 asterisks.
 */
```

Top-level comments:
The top level file comment is designed to orient readers unfamiliar with the code to what is in this file and any other disclaimers clients of the code should be given. It should provide a description of the file's contents and any dependencies or compatibility information. As an example:

coaches.js
```js
/**

 * Various components to handle management of lists of coaches for

 * the profile page.
 *
 * These utilities were not written to be a general purpose utility
 * for the entire code base, but has been optimized with the 
 * assumption that the Profile namespace is fully loaded.

 */
```

Class comments:
Classes must be documented with a description, and appropriate type tags (see “Methods and properties” comments for more information on types on the constructor.

```js
/**

 * Class making something fun and easy.

 * @param {string} arg1 An argument that makes this more interesting.

 * @param {Array.<number>} arg2 List of numbers to be processed.

 */

function SomeFunClass(arg1, arg2) {

  // ...

}
```

## 11) Methods and properties comments

All non-trivial methods and properties should also have JSDoc comments.
Type annotations are strongly encouraged; if there is even a slight chance that the type will be ambiguous to future readers, put in a type annotation.

Type annotations are based on the ES4/JS2 type system, and are documented in the Google JavaScript style guide.

`@param` and `@return` type annotations that have comments that do not fit on one line wrap to the next line and indent 4 spaces.

Example:

```js
/**
 * A UI component allows users to select badges from their full list
 * of earned badges, displaying them in a container.
 * Expects a Badges.BadgeList as a model.
 */
Badges.DisplayCase = Backbone.View.extend({
    /**
     * Whether or not this is currently in edit mode and the full
     * badge list is visible.
     */
    editing: false,

    /**
     * The full user badge list available to pick from when in edit mode.
     * @type {Badges.UserBadgeList}
     */
    fullBadgeList: null,

    /**
     * Enters "edit mode" where badges can be added/removed.
     * @param {number=} index Optional index of the slot in the display-case
     *     to be edited. Defaults to the first available slot, or if none
     *     are available, the last used slot.
     * @return {Badges.DisplayCase} This same instance so calls can be
     *     chained.
     */
    edit: function(index) {
    …
    },
   ...
};
```

## 12) Use “$” for jQuery

We use $ as the jQuery identifier, as opposed to typing out jQuery in full.

Bad:
```js
jQuery(".some-class span").hide();
```

Good:
```js
$(".some-class span").hide();
```

## 13) Avoid href="#" for javascript triggers

 When you want a link-like thing rather than a button to trigger a javascript operation, rather than going to a new address.

Here's a discussion on Stack Overflow about options:
http://stackoverflow.com/questions/134845/href-tag-for-javascript-links-or-javascriptvoid0


Bad:
```js
<a href="#">Flag</a>
Acceptable (if not totally "Good"):
<a href="javascript:void 0">Flag</a>
```


## 14) Use a new var statement for each declaration

Bad:
```js
var a = "foo",
    b = a + "bar",
    c = fn(a, b);
```
Good:
```js
var a = "foo";
var b = a + "bar";
var c = fn(a, b);
```

A single var statement is bad because:

if you forget a comma, you just made a global 
it originated when people wanted to save bytes, but we have a minifier
it makes line-based diffs/editing messier
it encourages C89-style declarations at the top of scope, preventing you from only declaring vars before first use, the latter preferable as it conveys intended scope to the reader
15) Don't pollute the global namespace, i.e. module.exports best practices

https://docs.google.com/a/khanacademy.org/document/d/13Sz-dGGzmH5oA7RcUTSz8vmGf770XXNAk7F8CBjeq3Q

Basically, use module.exports= and put it at the end of the file. It's best to have only one logical unit (function/class/component) per file.
