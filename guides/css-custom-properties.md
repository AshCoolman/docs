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

.desidious-sycamore {
  color: val(--leaves-color);
  display: inline-block;
  height: 75px;
  width: 75px;
}
.desidious-sycamore::after {
  content: ' ';
  background-color: val(--silver-grey);
}

.desidious-silver-birch {
  color: val(--leaves-color);
  display: inline-block;
  height: 100px;
  width: 50px;
}

.desidious-silver-birch::after {
  content: ' ';
  background-color: val(--silver);
}

.desidious-elder {
  color: val(--leaves-color);
  display: inline-block;
  height: 50px;
  width: 100px;
}

.desidious-elder::after {
  content: ' ';
  background-color: val(--beige-grey);
}
```
