http://philipwalton.com/articles/why-im-excited-about-native-css-variables/

* Syntax awkward, but future-proof
* CSS preprocessors
  * static
  * lexically scoped
* CSS custom properties:
  * Dynamic?
  * Scoped to DOM
  
https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables

CSS Variables: Allow use specific values throughout the doc
  
  ```css
  // Declaration
  element { --main-bg-color: brown; }
  // Using
  element { color: var(--main-bg-color); }
  ```
  * Don't need compilation
  * Names are semantic, expressive
  
  
CSS Variable:

1. Variables: identifier and value to replace regular values `var(--main-color)`
1. Custom properties, `--a-variable: 20px;`, additionally *subject to cascade & inheritance*

Simple use:

```css
:root {
  --leaves-color: green;
  --silver-grey: grey;
  --silver: silver;
  --beige-grey: beige;
}

.decidious-sycamore {
  color: var(--leaves-color);
  display: inline-block;
  height: 75px;
  width: 75px;
}
.decidious-sycamore::after {
  content: ' ';
  background-color: var(--silver-grey);
}

.decidious-silver-birch {
  color: var(--leaves-color);
  display: inline-block;
  height: 100px;
  width: 50px;
}

.decidious-silver-birch::after {
  content: ' ';
  background-color: val(--silver);
}

.decidious-elder {
  color: var(--leaves-color);
  display: inline-block;
  height: 50px;
  width: 100px;
}

.decidious-elder::after {
  content: ' ';
  background-color: var(--beige-grey);
}
```

http://philipwalton.com/articles/why-im-excited-about-native-css-variables/

What preprocessors can't do:

* Can't have logic dynamics
SASS can only setup "gate" conditions, and then walk away. It can't watch and react to dynamics.
```
$gutter: 1em; 
@media (min-width: 30em) {
 $gutter: 2em; /* ASSIGNMENT FAILS */
 text: red; /* Rules applies */
}
.thing {
  padding: $gutter; /* Always 1em */;
 }
```

* Cascade: Again, DOM is dynamic, you cannot cascade on DOm element
```
$font-size: 1em;

.user-setting-large-text {
  $font-size: 1.5em; /* this relies on dom element, doesn't exist to SASS */
}
body {
  font-size: $font-size;
}
```

* Preprocessor _variables_ don't inherit:
```
PSUEDOCODE!!
.alert { background-color: lightyellow; }
.alert.info { background-color: lightblue; }
.alert.error { background-color: orangered; }

.alert button {
 /* Doesn't work, as it relies on html cascading the color into it*/
 border-color: darken(background-color, 25%);
}
```

Discussion: So this is relient on the structure. Ah I'm starting to see what is going on. You can bake some stuff, so it stays the same, but parametised. But for those occasional, and big styling problems which are based on the dom, we now have great solution. Then, for styling - we get more complexity (and probably some more self-foot-shooting). At very least, javascript will not be the only expressive way to fix some of those other-wise difficult problems.

* Preprocessor variables aren't interoperable: Can't less+sass+postcss



