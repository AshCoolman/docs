---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-19 13:36:22
categories: react, react-native, ios, android
---

**DRAFT: Code samples are indicative only, and may include syntax errors. Please excuse any grammer or spelling sillyness**

# Cross-platfrom web/native development with React & React Native

Every line of code is a liability, but not every line creates value. This is particuarly interesting when it comes to building native & web apps that offer the same features. Theoretically _all_ domain code can be shared beween _all_ platforms. That is to say, all platforms have the concept of the "Login button", and a "button pressed" event. But different platforms render the button and handle its events differently. 

Rebuilding React web apps in [React Native](https://facebook.github.io/react-native/) has great code/effort reuse potential. I've been building a protoype that **takes code from an existing web app, and reuses it in a native app to provide the same features**. After rebuilding two features, I've settled on a technique, which I will describe in this article.

# React recap

_Skip this section if you are already comfortable with React_

React components map state to a "virtual-DOM" (a _View_), which is then mapped to platform elements for rendering. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious platform targets. But it is also illustrative to know the virtual-DOM can also be linked to completely different rendering pipelines e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 


# Cross platform code

All cross-platform code must contain the following _conceptual logic_ somewhere:

```sh
### Pseudocode! ###
IF platform is 'web' THEN
  browser.render '<button onClick="doLogin" />'
ELSE IF platform is 'native' THEN
  native.render '<TouchableHighlight onPress="doLogin" /> />'
ENDIF
```

But where is the best place for this? There are a number of approaches (that can also be blended). Here is a quick run down of the approaches I considered:

## Approach one: runtime detection

### Basic idea

Using expressions in view logic render different markup per platform

### Conclusion

Expressions could end up ANYWHERE. And logically, the platform is not a run-time variable, so it should not be treated as such.

## Approach two: 1:1 mapping of platform implementations

### Basic idea

```sh
  ### Pseudocode! ###
  mapping = {
    web: {
      div: 'div',
      span: 'span'
    },
    native: {
      div: 'View',
      span: 'Text'
    }
  }
  
  React.createElement(
    mapping[platform]['div']
  );
}
```

So `div` always maps to `View`, hence 1:1 mapping.

### Details

There are a number of ways to do this, incbut one quite amazing library that can help is [react-native-web](https://github.com/necolas/react-native-web).  The project [self describes its use case](https://github.com/KodersLab/react-native-for-web#why-use-react-native-for-web) as cheap cross-platform with a [limited palette](https://necolas.github.io/react-native-web/storybook/?selectedKind=APIs&selectedStory=Clipboard&full=0&down=1&left=1&panelRight=0&downPanel=kadirahq%2Fstorybook-addon-actions%2Factions-panel). It provides [react-native elements & API (including styles!! :fire:)](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web - e.g. a `View` component for the browser that supports _exactly_ the same style attributes as the React Native version. This means almost _effortless_ conversion from a ReactNative app to a web app.

### Conclusion

I did not try out this technique.

This is partly because I'm building with the **reverse workflow, Web to Native**. 

But also because coupling `View` to a _react-native-web_'s implementation (even if it is amazing) seems too rigid to achieve the design I want. This coupling have immediately introduce some hurdles:

 e.g. 
 - The limited palette would need to be extended e.g. Checkbox
 - No clear path to use UI framework
 - library encourages "App-like" unified designs, while I'm coing for  vastly different designed for mobile vs web
 
Even though this technique is misaligned with the goals of the prototype, it is a great approach - largely due to _react-native-web_, and I'd like to try this in the future.

## Approach three: Componenents parametised by platform

### Basic idea

A Component is a function, if we want to partially use a function (domain), but be able to parametise it later (platform) there is a pattern for that, the [factory function](https://www.ibm.com/developerworks/library/j-ft10/):

```js
  // ### Psuedocode! ###
  function factory({Wrapper, Label, Button}) {
    const Component = props => (
      <Wrapper>
        <Label>Hello</Label>
        <Button>Go</Button>
      </Wrapper>
    );
    return Component;
  }
  const CompWeb = factory({
    Wrapper: div,
    Label: span,
    Button: button
  });
```

Depending on your programming-heritage/preference, you might refer the _factory_ as a [partial](https://medium.com/functional-javascript/higher-order-functions-78084829fff4) instead.

### Details

#### Simple example

##### Step 1: Platform agnostic component creation function:

```js
// LinkFactory.js
// * No platform elements are specified (e.g. div)
// * No platform apis are specified (e.g. window.alert)
// * No styling in component
function LinkFactory({ Wrapper, Hat, Speech, doSay }) {

  const NewLink = props => (
    <Wrapper>
      <Hat />
      <SayButton onClick={()=> props.doSay('Where is Zelda')}>
        {props.children}
      </SayButton>
    </Wrapper>
  );
  return NewLink;
}

export default LinkFactory;
```

##### Step 2: Creating the Link, with browser elements:

```js
// Link.web.js
// * `SayButton` props are spread onto the element, including children
// * WARNING: Each component with a _style attribute_ needs css() called on it (one in this example)
import css from 'react-css-modules';
import style from './light.css';
import LinkFactory from './LinkFactory';

export default LinkFactory({
  Wrapper: css( ()=><div/>, style ),
  Hat:  ()=><i icon="hat" />,
  SayButton: props=><button {...props} />
  doSay: window.alert
})
```

##### Step 3: Creating the `Link`, with React Native elements

```js
// Link.native.js
// * `Wrapper` is using styled-components instead of react-css-modules
// * `SayButton` in the virtual DOM maps to multiple platform tags
// * WARNING: The Text in `SayButton` unnessecarily has `onClick` spread onto it
import LinkFactory from './LinkFactory';
import styled from 'styled-components/native';
import { Text, TouchableOpacity as Touch, Alert } from 'react-native';

export default LinkFactory({
  Wrapper: styled.View`
    background-color: green;
  `,
  Hat:  () => <Text>'▲'</Text>,
  SayButton: props => <Touch onPress={props.onClick}>
    <Text {...props} />
  </Touch>,
  doSay: Alert.alert /* Uses dialogue heading */
})
```

#### ASIDE: Are Factories Higher order components?

> A higher-order component is a function that takes a component and returns a new component.

－ [React docs](https://facebook.github.io/react/docs/higher-order-components.html)

By a strict interpretation, _potentially_ - it takes a hash of components.

> Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions.

－ [Eloquent javascript](http://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK)

But they are definately Higher-order functions

#### Class component example

```js
// LoginFactory.js
import api from 'api';

function LoginFactory({ Wrapper, Header, Button })  {
  class Login extends React.Component {
    constructor() {
      super();
    }
    doLogin = () => {
      this.setState({ isLoading: true });
      api
        .post(this.context.loginDetails)
        .then(this.handleApiSuccess)
        .catch(this.handleApiError)
        .finally(this.handleApiFinally);
    }
    handleApiSuccess =  => {
      this.setState({ err: false });
      this.context.router.push('/home');
    }
    handleApiError = err => {
      this.setState({ err });
    }
    handleApiFinally = () => {
      this.setState({isLoading: false});
    }
    render() {
      return (
        <Wrapper>
          <Header>Press to login</Header>
          <Button 
            onClick={this.doLogin}
            disabled={this.state.isLoading}
          >
            {'Login'}
          </Button>
          <Error>{this.state.err}</Error>
        </Wrapper>
      )
    }
  }
  return Login; // Factory returns newly parametised component
}
```

```js
// Login.web.js
import styled from 'styled-components';
const Login = LoginFactory({
  Wrapper: p=><div {...p} />,
  Header:p=><h1 {...p} />,
  Button: p=><button {...p} />,
  Error: styled.span`
    color: red;
    `
})
ReactDOM.render(<Login />);
```

```js
// Login.native.js
import { View, Text, TouchableOpacity as Touch} from 'react-native';
import styled from 'styled-components/native';
const Login = LoginFactory({
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>,
  Error: styled.Text`
    color: red;
    `
});
AppRegistry.registerComponent(<Login />);
```


### Conclusion

This was the first technique I first reached for, and never found a reason to change. I pushed and pulled the features, but seemed to always find an elegant solution. For one feature I was quite hard nosed about getting a very accurate version of the web app, for another feature I signifigantly changed the behavior and added behavior so the feature better sympathised with iOS.

Best thing:

* Native dev reuses all domain code, including View

Good things:

* Its just functions™, quite "React idiomatic"
* Clear seperation of domain vs platform code
* Easy to _not_ reuse when appropriate
* Reusing all apps

Worst thing:

* Code highly-coupled to browser must be uncoupled

Bad things:

* Must wrap platforms apis
* Functions creating functions may be idiomatic, but it is _Advanced_

# FAQ

#### Reusing everything seems rigid - did you always reuse web code?

Almost never on an [atomic level](http://bradfrost.com/blog/post/atomic-web-design/#atoms), as this is closely tied to the platform.

On a [molecular level](http://bradfrost.com/blog/post/atomic-web-design/#molecules), reuse happens ~90-95% of the time.
The Main Nav and routes have **not** been reused - as they are not particularly complex.

#### Whats the process for refactoring into Component + Factory?

I start with the container, and traced down to the lowest level components change them to use Factories. Then for each web component, I do the following tested steps:

1. Create a Factory that creates the Component as-is
2. Gradually move platform tags from Factory, into the Component's factory call (leave platform-agnostic tags in the Factory).
3. Extract any browser APIs from the Factory, and pass them in as wrapped functions/options.
4. Make sure all text is wrapped in markup (ReactNative does not allow unwrapped text)

#### Whats the process for using Factory to create Native Component?

I start with the highest level container, then implement down.

1. Create new Component (Factory call with stubs)
2. Gradually implement each stub as:
  * platform element/API OR
  * new Component (Factory call with stubs) REPEAT STEP 1

#### What is the developer experience with large components?

After creating the Factory, native development is business-as-usual, except you don't worry about domain logic. You only need to worry about which native elements to use, and creating react versions of platform APIs.

#### How is the project organised?

Made an entirely new project for native, that has an npm dependency on the web project. Because `npm link` is not supported by the build system in, I've had to so some hacks. This probably won't scale to teams in its current form. Will either unify the code base, or improve the build system

# Ongoing Questions

* How much confidence does the existing web unit/component tests give?
* What is the DX of selenium tests for native apps? (can e2e tests be reused?)
* How to best organise multi-platform codebases?
* Would [Haul](https://github.com/callstack-io/haul) be a better alternative than packager?

# The prototype

## Existing web app

Prior work: 

* 2 months of coding (~ 12k LOC)
* ~ 80% test coveragef
* limited mobile designs

Tech used:

* react-router-native
* redux
* styled-components
* react-bootstrap

## Web features ported to React Native

The first feature is a list view:

* ~ 2 weeks dev time
* Major components: 1 x container, 1 x stateless component
* Major features: infinite scrolling

The second feature is a detail view:

* ~ 4 weeks dev time
* Major components: 2 x containers, ~10 x stateless
* Major features: includes "read more" button that only appears if text is long enough, markdown rendering, and position fixed boxes

