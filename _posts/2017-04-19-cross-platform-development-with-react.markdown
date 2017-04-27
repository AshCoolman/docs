---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---


**DRAFT: Code samples are indicative only, and may include syntax errors. Please excuse any grammer or spelling sillyness**

# Intro

Every line of code is a liability, but not every line creates value. 

Rebuilding React web apps in [React Native](https://facebook.github.io/react-native/) has great code/effort reuse potential. I've been building a protoype that does exactly that. After rebuilding two features, I've settled into a very technique that allows for 1:1 component reuse, but can be relaxed for certain components.

## Cross-platform React

Theoretically _all_ domain code can be shared beween _all_ platforms. That is to say, all platforms have the concept of the "Login button", and a "button pressed" event. But different platforms render the button and handle its events differently. 

React components map state to a "virtual-DOM" (a _View_), which is then mapped to platform elements for rendering. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious platform targets. But it is also illustrative to know the virtual-DOM can also be linked to completely different rendering pipelines e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 

All cross-platform code must contain the following _conceptual logic_ somewhere:

```sh
IF platform is 'web' THEN
  render('<button onClick="doLogin">Login</button>');
ELSEIF platform === 'native-app' THEN
  render('<TouchableHighlight />');
ENDIF
```

But where is the best place for this?

#### Option A: Map 1×Native to 1×Web

Mapping one React Native element to one browser element can be achieved in many ways - a hash object, a Higher order component or a library. A particularly mature & powerful library is [react-native-web](https://github.com/necolas/react-native-web). provides [react-native elements & API (including styles!! :fire:)](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web, allowing React Native apps to produce web apps, with almost no effort.

The prototype I'm buiding needs **the reverse workflow, web to native**, so its not really appropriate. But regardless, there are  serious styling/semantics limitations in using this project (and technique). The project [self describes its use case](https://github.com/KodersLab/react-native-for-web#why-use-react-native-for-web) as cheap cross-platform with a [limited palette](https://necolas.github.io/react-native-web/storybook/?selectedKind=APIs&selectedStory=Clipboard&full=0&down=1&left=1&panelRight=0&downPanel=kadirahq%2Fstorybook-addon-actions%2Factions-panel). 

#### Option B: Platform switching logic

Using expressions to render different markup per platform is possible. But it means expressions could end up ANYWHERE. And logically, the platform is not a run-time variable, so it should not be treated as such.

#### Option C: Use domain components parametised by platform

NOTE: There are a few ways to acheive this - described is just the "simplist" idiomatic technique I first reached for - and never found a reason to change

##### React recap

React components are functions that build virtualDOM (domain), which maps to elements (platform). We can use _another function_ to create a React component ([factory function](https://www.ibm.com/developerworks/library/j-ft10/) _parametised_ with platform elements. Depending on your programming-heritage/preference, you might refer the _factory_ as a [partial](https://medium.com/functional-javascript/higher-order-functions-78084829fff4) instead.


##### ASIDE: Decouple style only

This is how to us idiomatic React to **decouple style from the `Link` component**

```js
// Creates two identical components apart from styles
import css from 'react-css-modules'; // Higher order component
import styles from './light.css';

const Link = props => (
  <div style="wrapper">
    <i icon="hat" />
    <i icon="sword" />
    <button onClick={()=>window.alert('Where is Zelda?')}>Say it</button>
  </div>
);

export const LightLink css(Link, styles);
```

Now for this protoype, I wanted to **decouple the platform-elements** from the Component.

##### Step 1: Platform agnostic component creation function:

```js
// LinkFactory.js
// * No platform elements are specified (e.g. div)
// * No platform apis are specified (e.g. window.alert)
// * No styling in component
function LinkFactory({ Wrapper, Hat, Sword, Speech, doSay }) {

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
  doSay: Alert.alert // Uses dialogue heading
})
```
Real world refinements assuming use of UI frameworks for [native](https://docs.nativebase.io/) and [web](http://www.material-ui.com/#/components/app-bar) and Api wrappers:

```js
// Link.native.js
import LinkFactory from './LinkFactory';
import { View as Wrapper, Button as SayButton } from '../native/components';
import { alert as doSay } from '../native/apis';
export default LinkFactory({ Wrapper, Hat, SayButton, doSay });
```

```js
// Link.web.js
import LinkFactory from './LinkFactory';
import { View as Wrapper, Button as SayButton } from '../web/components';
import { alert as doSay } from '../web/apis';
export default LinkFactory({ Wrapper, Hat, SayButton, doSay });
```


##### ASIDE: Are Factories Higher order components?

> A higher-order component is a function that takes a component and returns a new component.

－ [React docs](https://facebook.github.io/react/docs/higher-order-components.html)

> Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions.

－ [Eloquent javascript](http://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK)

By a strict interpretation of the React docs, no. But regardless, they are still higher order _functions_.

##### Class component example

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
const Login = LoginFactory({
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>
});
AppRegistry.registerComponent(<Login />);
```
##### FAQ

> Do you always reuse web code, that seems rigid?

Almost never on an [atomic level](http://bradfrost.com/blog/post/atomic-web-design/#atoms), as this is closely tied to the platform.

On a [molecular level](http://bradfrost.com/blog/post/atomic-web-design/#molecules), reuse happens ~90-95% of the time.
The Main Nav and routes have **not** been reused - as they are not particularly complex.

 NOTE: As this is an experiment I've pushed the 1:1 reuse harder than you might do in production i.e. _to the point of inconvenience_. And I've also changed web layouts and behavior to be optimised for native. 


> Whats the process for refactoring into Component + Factory?

I start with the container, and traced down to the lowest level components change them to use Factories. Then for each web component, I do the following tested steps:

1. Create a Factory that creates the Component as-is
2. Gradually move platform tags from Factory, into the Component's factory call (leave platform-agnostic tags in the Factory).
3. Extract any browser APIs from the Factory, and pass them in as wrapped functions/options.
4. Make sure all text is wrapped in markup (ReactNative does not allow unwrapped text)

> Whats the process for using Factory to create Native Component?

I start with the highest level container, then implement down.

1. Create new Component (Factory call with stubs)
2. Gradually implement each stub as:
  * platform element/API OR
  * new Component (Factory call with stubs) REPEAT STEP 1

> So what does this look like at scale?

After creating the Factory, native development is business-as-usual, except you don't worry about domain logic. You only need to worry about which native elements to use, and creating react versions of platform APIs.

> How is the project organised?

Made an entirely new project for native, that has an npm dependency on the web project. Because `npm link` is not supported by the build system in, I've had to so some hacks. This probably won't scale to teams in its current form. Will either unify the code base, or improve the build system

## Cross-platform prototype developer experience

### Using Component Factories

The best thing:

* Optional reuse of domain code including the view

Good things:

* Its quick
* ComponentFactory markup is purely domain, Component markup is purely presentational
* You constantly decouple out of nessecity (web-componenty)
* Basic refactoring into Factories is easy
* You have 100% flexibility (1:N) of elements, styles and props

The worst thing:

* Working with existing code can be hard:
  * Non-semantic web markup has to be made semantic
  * Code highly-coupled to browser can be hard be uncouple

Bad things:

* You must be able to wrap all platforms apis (mostly easy, but sometimes complicated)
* Functions creating functions is idiomatic React, but its still Advanced

### Using React Native

Best thing: requirement-comprehension-cost is paid once and QA effort is (much?) lower

Worst thing: the developer experinces is shaky
## Ongoing Questions

* How much confidence does the existing web unit/component tests give?
* What is the DX of selenium tests for native apps? (can e2e tests be reused?)
* How to best organise multi-platform codebases?
* Would [Haul](https://github.com/callstack-io/haul) be a better alternative than packager?

## The prototype

#### Existing web app

State of current web app: 

* 2 months of coding (~ 12k LOC)
* ~ 80% test coverage
* limited mobile designs

Tech used:

* react-router-native
* redux
* styled-components
* react-bootstrap

#### Web features ported to React Native

Still under QA

The first feature is a list view:

* ~ 2 weeks dev time
* Major components: 1 x container, 1 x stateless component
* 1 API endpoints
* infinite scrolling

The second feature is a detail view:

* ~ 4 weeks dev time
* Major components: 2 containers, ~10 x stateless
* 2 x API
* includes "read more" button that only appears if text is long enough, markdown rendering, and position fixed boxes

## Technology notes

#### [ReactNative](https://facebook.github.io/react-native/)

Compared to React web developement:

* Shakey developer experience - a few strange problems with strange solutions, frequent crashes
* Does not use webpack, uses packager instead (watchman)
* Slightly more difficult to debug than web


#### [Expo](https://docs.expo.io/versions/v16.0.0/index.html) (via create-react-native-app)

Incredible improvments for

* Stability
* developers
* QA testers (test link from QR code)

#### [styled-components](https://github.com/styled-components/styled-components)

A natural fit for `<component>Factory`

#### [react-base](https://nativebase.io/)

At the time of this writing, of all React Native UI frameworks:

* expressive markup
* solid docs &  community support
* theming method is quite a buy-in ( we will probably transition away)
