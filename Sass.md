# The Benefits of Sass
- **Variables**: for reusable values such as colors, font-sizes, spacing, etc...
- **Nesting**: to nest selectors inside of one another, allowing us to write less code
- **Operators**: for mathematical operations right inside of CSS
- **Partials and imports**: to write CSS in different files and importing them all into one single file
- **Mixins**: to write reusable pieces of CSS code
- **Functions**: similar to mixins, with the difference that they produce a value that can then be used
- **Extends**: to make different selectors inherit declarations that are comman to all of them
- **Control directives**: for writing complex code using conditionals and loops

The difference between Sass and SCSS is that Sass is the original version of the language and it doesn't contain curly braces for blocks and doesn't have semicolons to end lines. As this makes it a bit harder to convert existing CSS files, most people use SCSS as it has these features just like CSS.

# Features

## Variables
https://sass-lang.com/documentation/variables
``` SCSS
$color-primary: #fe0303;

button {
    background-color: $color-primary;
}
```

## Comments
https://sass-lang.com/documentation/syntax/comments
``` SCSS
//single-line commenting
//anything after this (on this line) is a comment
//this comment will not show up in CSS
/*this comment will be in the CSS if not compressed*/
/*!this comment will always be in the CSS*/
```

## Nesting
https://sass-lang.com/documentation/style-rules#nesting
``` SCSS
.navigation {
	float: left;
	
	li {
		display: inline-block;
		
		&:first-child { // & is as though it was li
			margin: 0;
		}
		
		a:link {
			color: $color-text-dark;
		}
	}
}
```

## Color Functions
https://sass-lang.com/documentation/modules/color
``` SCSS
.btn-main {
    background-color: darken($color-secondary, 15%);
}

.btn-hot {
    background-color: lighten($color-secondary, 15%);
}
```

## Mixins
https://sass-lang.com/documentation/at-rules/mixin

Allows you to reuse a section of code
``` SCSS
@mixin clearfix {
	&::after {
		content: "";
		clear: both;
		display: table;
	}	
}

nav {
	margin: 3rem;
	background-color: $color-primary;
	
	@include clearfix;
}
```
This one shows the use of variable arguments:
``` SCSS
@mixin style-link-text($col) {
	text-decoration: none;
	text-transform: uppercase;
	color: $col;
}

a:link {
    @include style-link-text($color-text-dark);
}
```

## Functions
https://sass-lang.com/documentation/at-rules/function
``` SCSS
@function divide($a, $b) {
    @return $a / $b;
}

div {
    margin: divide(60, 2) * 1px; //this is a best practice to convert the number to pixels
}
```

## Extend
Useful to prevent you from having to write duplicate selectors
### Before
``` SCSS
.btn-main,
.btn-hot {
    padding: 1rem;
    border-radius: 5px;
}

.btn-main {
    background-color: blue;
}

.btn-hot {
    background-color: red;
}
```
### After
``` SCSS
%btn-placeholder {
    padding: 1rem;
    border-radius: 5px;
}

.btn-main {
    @extend %btn-placeholder;
    background-color: blue;
}

.btn-hot {
    @extend %btn-placeholder;
    background-color: red;
}
```

This could have been accomplished with a mixin in the same way. The difference is how the code is compliled. Using `@extend` will result in the same code as the _Before_ block and a mixing would give **completely duplicated** declarations.

Another way to think about this is that mixins copy their _code_ to a location and extends copy the _selectors_ to the code.