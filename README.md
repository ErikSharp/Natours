# How CSS works behind the scenes

1. Load HTML
1. Parse HTML
    - This creates the DOM
1. Load/Parse CSS
    1. Resolve conflicts in the declarations (cascade)
    1. Process final CSS values
        - Converting of units like 50% to an actual value
    1. Data is stored in the CSS Object Model (CSSOM)
1. Combine the DOM and the CSSOM to create the _Rendered Tree_
1. Website rendering - the visual formatting model
1. Final render

# CSS parsing phase
- First C is Cascade
    - This is resolving all the conflicting CSS rules in the following order:
        1. Importance
            - Evaluation order
                1. User `!important` declarations
                1. Author `!important` declarations
                1. Author declarations
                1. User declarations
                1. Default browser declarations
        1. Specificity
            - Evaluation order and values
                1. Inline styles (written in HTML) - highest weight
                1. IDs
                1. Classes, pseudo-classes, attributes
                1. Elements, pseudo-elements - lowest weight
            ```CSS
            /* this would be worth 0,0,1,0 */
            .button {
                background-color: blue;
            }

            /* this would be worth 0,1,2,2 */
            nav#nav div.pull-right .button {
                background-color: green;
            }

            /* this would be worth 0,0,0,1 */
            a {
                background-color: purple;
            }

            /* this would be worth 0,1,2,1 */
            #nav a.button:hover {
                background-color: yellow;
            }
            ```
            - Evalation is done from left (highest weight) to right (lowest weight)
                - I used to think that you could just add them up with the lowest being 1 and the highest being 1000. It doesn't work this way, but rather checking the first column and if there is a tie then it proceeds to the next. This prevents there from being 11 classes that would beat a single ID. The ID always wins.
        1. Order of declarations
            - If in the end the specificity is the same, then the last declaration wins
    - The universal selector `*` has no specificity value
    - You should always rely on more specificity than on the order of selectors
    - The only tim ethat you should rely on declaration order is with external 3rd-party stylesheets
        - You should make sure that yours comes last

# Spcificity example
Consider the following three rules:

``` CSS
#nav div.pull-right a.button {
    background-color: orangered;
}

#nav div.pull-right a.button:hover {
    background-color: green;
}

#nav a.button:hover {
    background-color: green;
}
```

If just rules 1 and 3 were present, then the hover color would never be shown because it has a lower specificity than number 1.

The fix for this problem was to create rule number 2 which has a higher specificity than number 1.

# How CSS values are processed
![How CSS values are processed](/src/img/HowCSSValuesAreProcessed.png)

- Each property has an initial value, used if nothing is declared (and if there is no inheritance)
- Browsers specify a root `font-size` for each page (usually 16px)
- Percentages and relative values are always converted to pixels

# How units are converted from relative to absolute (pixels)

Regarding the following HTML:
``` HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <header>
        <div class="header-child"></div>
    </header>    
</body>
</html>
```
![Converting units](/src/img/ConvertingUnits.png)


- Percentages are measured relative to their parent's **`font-size`**, if used to specify `font-size`
- Percentages are measured relative to their parent's **`width`**, if used to specify lengths
- `em` are measured relative to their **parent** `font-size`, if used to specify `font-size`
- `em` are measured relative to the **current** `font-size`, if used to specify lengths
- `rem` are always measured relative to the **document's root** `font-size`
- `vh` and `vw` are simply percentage measurements of the viewport's `height` and `width`

# Inheritance
![Inheritance](/src/img/Inheritance.png)
- Notice that the computed `line-height` is 30px which is 150% of the *parent's* `font-size` and **NOT** of the *child*
- Properties related to text are inherited
    - `font-family`
    - `font-size`
    - `color`
- The computed value of a property is what gets inherited, **not** the declared value
- The `inherit` keyword forces inheritance on a certain property
- The `initial` keyword resets a property to its initial value

# The REM values trick
It is a good practice to use `rem` values everywhere instead of `px` (pixel) values.
1. Create the following rule
``` CSS
html {
    /* We make the base 10px to make the math easy - 1rem = 10px
    But, making in 10px here would mean that the user couldn't override
    By setting a percentage here, it takes from the browser value which 
    by default is 16px unless the user overrides. */

    /* This value represents the math to get to the equivalent 10px */
    font-size: 62.5%;
}
```
2. Everywhere that there is a `px` (pixel) value, convert to `rem`
``` CSS
.logo {
    /* This used to be 35px and since the base is 10px, just move the decimal */
    height: 3.5rem;
}
```
3. Now you can easily scale the entire app with the root font-size

# Display property

## Block
- Elements formatted visually as block
- 100% of parent's width
- Vertically one after another
    - They produce line breaks after themselves
- Box-model is applied

## Inline
- Content is distributed in lines
- Occupies _only_ content's space
- No line breaks
- No heights and widths
- Margins and padding horizontal only

## Inline-block
- A mix of block and inline
- Occupies _only_ content's space (same as inline)
- No line breaks (same as inline)
- Box-model is applied (same as block)

# Positioning Schemes

## Normal Flow
- Default positioning scheme
- **NOT** floated
- **NOT** absolutely positioned
- Elements are laid out according to their source order
- If relative, it also acts as normal

## Floats
- Element is removed from the normal flow
- Shifts as far as possible to the edge of the containing element or another floated element
- Text and inline elements will wrap around the floated element
- The container will not adjust its height to the element
    - A clear fix is used to prevent this
``` CSS
float: left;
float: right;
```

## Absolute positioning
- Element is removed from the normal flow
- No impact on surrounding content or elements
- We use `top`, `bottom`, `left`, and `right` to offset the element from its relatively positioned container
``` CSS
position: absolute;
position: fixed;
```

# BEM Block Element Modifier
This is a naming strategy that has the following form:

``` CSS
.block {}
.block__element {}
.block__element--modifier
```

- A block class might be `.btn` as this would be a standalone component that is meaningful on its own
- An element would be something like `.btn__info` as `info` has no meaning on its own
- A modifier would be something like `.btn__info--round` as it is just a different version
    - The modifier could also be directly on the block as `.btn--round` if necessary
- Any multi-word name uses a hyphen such as `.btn__name-stats--round`

# Physical Architecture
## The 7-1 Pattern
- 7 different folders for partial Sass files
    1. `base/`
        - base product definitions
    1. `components/`
        - one file for each component
    1. `layout/`
        - project layout definition
    1. `pages/`
        - styles for specific pages
    1. `themes/`
        - different visual themes
    1. `abstracts/`
        - code that doesn't output CSS such as variables and mixins
    1. `vendors/`
        - 3rd party CSS
- 1 main Sass file to import all other files into compiled CSS