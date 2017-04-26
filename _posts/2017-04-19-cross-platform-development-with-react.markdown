---
layout: post
title:  "Pig-headed cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---

### Intro

Every line of code is a liability, but not every line creates value. 

This line of reasoning, makes the code reuse opporutinities given by [React Native](https://facebook.github.io/react-native/) very interesting. I've working on a protoype that takes web app code, and maximally reuses code (and QA). Its been very promising, but I'm still closing in on optimimal methodologies.

### The prototype

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

#### Rational

I converted two features to the app, using mobile designs were they existing and elborating were they didnt. QA is being undergone as a formal process, but we are not being "production-strict". The emphasis has been on getting a effort-efficient NativeApp that creates value for our customers - rather than pushing for a magical experience.

#### Web features ported to React Native

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

### Cross-platform React

Theoretically _all_ domain code can be shared beween all platforms. That is to say, all platforms have the concept of the "Login button", and a "button pressed" event. But different platforms render the button and hook into its events differently. 

React components build a "virtual-DOM", which is then mapped to platform elements for rendering. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious platform targets. But it is also illustrative to know Component's logical _View_ can also be linked to completely different rendering pipelines e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 

All cross-platform code must contain the following _conceptual logic_ somewhere:

```sh
IF platform is 'web' THEN
  render('<button onClick="doLogin">Login</button>');
ELSEIF platform === 'native-app' THEN
  render('<TouchableHighlight />');
ENDIF
```

But where is the best place for this?

###### Option A: Map 1 x Native : 1 x Web

Mapping one React Native element to one browser element can be achieved in many ways - a hash object, a Higher order component or a library. A particularly mature & powerful library is [react-native-web](https://github.com/necolas/react-native-web). provides [react-native elements & API](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web, allowing React Native apps to produce web apps, with almost no effort.

Our prototype needed the reverse workflow - but regardless, there are serious limitations that can effect styling and semantics. The project [self describes its use case](https://github.com/KodersLab/react-native-for-web#why-use-react-native-for-web) as cheap cross-platform with a [limited palette](https://necolas.github.io/react-native-web/storybook/?selectedKind=APIs&selectedStory=Clipboard&full=0&down=1&left=1&panelRight=0&downPanel=kadirahq%2Fstorybook-addon-actions%2Factions-panel). 

###### Option B: platform switching expressions in render logic

Using expressions to render different markup per platform is possible. But messy. Plus, the platform is not a run-time variable, so it should not be treated as such.

###### Option C: Use domain components parametised by platform

React components are functions that build virtualDOM (domain), which maps to elements (platform). We can use _another_ [function to create a React component](https://www.ibm.com/developerworks/library/j-ft10/) _parametised_ with platform elements. Depending on your programming-heritage, you may refer to this _creating function_       as a `<component>Factory` or a [`<component>Partial`](https://medium.com/functional-javascript/higher-order-functions-78084829fff4) in the example below:


First, lets look at how you can use idiomatic React to decouple style from a `Link` component using `react-css-modules`:

```js
import css from 'react-css-modules';
import lightStyles from './light.css';
import darkStyles from './dark.css';

const Link = props => (
  <div style="wrapper">
    <i icon="hat" />
    <i icon="sword" />
    <button onClick={()=>window.alert('Where is Zelda?')}>Say it</button>
  </div>
);

// Two components exactly the same, but with different styles
export const LightLink css(Link, lightStyles);
export const DarkLink = css(Link, darkStyles);
```

Note that `css(..., ..)` is a [Higher order component](https://facebook.github.io/react/docs/higher-order-components.html)

Now for this protoype, I wanted to decouple the platform-elements from the `Link`.

####### Step 1: Platform agnostic component creation function:

```js
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

So now we have a function, that returns new `Link` components when you call it. Please take care to note:

* No platform elements are specified (e.g. div)
* No platform apis are specified (e.g. window.alert)
* No styling in component
* Component names are entirely 

####### Step 2: Creating the Link, with browser elements:

```js
// Link.web.js
import css from 'react-css-modules';
import lightStyles from './light.css';
import LinkFactory from './LinkFactory';

export default LinkFactory({
  Wrapper: css( ()=><div/>, lightStyles ),
  Hat:  ()=><i icon="hat" />,
  SayButton: props=><button {...props} />
  doSay: window.alert
})
```

Please note:

* `SayButton` props are spread onto the element, including children
* WARNING: Each component with a _style attribute_ needs css() called on it (one in this example)

####### Step 3: Creating the `Link`, with React Native elements

```js
// Link.native.js
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

Please note:

* `Wrapper` is using styled-components instead of react-css-modules
* `SayButton` in the virtual DOM maps to multiple platform tags
* WARNING: The Text in `SayButton` unnessecarily has `onClick` spread onto it


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

####### Discussion: Are these Higher order components?

> A higher-order component is a function that takes a component and returns a new component.
- React docs

> Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions.
- [Eloquent javascript](higher order function](http://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK)





```js
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

Web implementation:

```js
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

Native implementation:

```js
import { View, Text, TouchableOpacity as Touch} from 'react-native';

const Login = LoginFactory({
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>
});
AppRegistry.registerComponent(<Login />);
```

### What did I do exactly for the prototype

I converted two large React container components (and ~ 23 stateless components) from the web project. I relied only on the technique described in option C, and tried to do an close-approximation of the web design converted to native. Both features used a subset of "[atomic](http://bradfrost.com/blog/post/atomic-web-design/#atoms)" components (Buttons, Headings, Icons, ErrorText etc).

The first feature used 1 container and 1 signifigant "[molecule](http://bradfrost.com/blog/post/atomic-web-design/#molecules)" and had an infinite-scroll behavior.

The second feature was a huge pain to build for web, as it had 2 containers, but ~ 14 signifigant stateless components. All three of the frontend developers on my team had worked on this. Signifigant features included a "read more" button that would only appear if the text was long enough, markdown rendering, and a heap of custom atoms for lists etc. 

### What was it like doing this for real


The first feature was very easy - including the _equivalent_ scrolling logic for Native (which took ~ 1.5hrs).

The second feature was a bit of a grind - just for the shear number of components. I would say it took approx 50% of its web build time??? And much, much, much easier - mainly down to requirements being clear.

Much of the development time was spent not developing features:

* Refactoring the web
* Setting up the project
* Building mock Nav
* Testing techniques & technologies
* Learning the React Native (API, [native-base theming](https://nativebase.io/), overcoming ReactNative instability) 

I expect a futher signigant increase in dev speed as the whole process settles.

**TODO CHECK COMMITS TO RECONSTRUCT TIMELINE**

#### Analysis of Cross-platform Factory technique

Good things:

* ComponentFactory markup is purely domain
* Component markup is purely presentational (feels so sensible)
* You constantly decouple out of nessecity, which a great code trait to have in this webcomponenty world
* Refactoring is pretty simple and brainless (largely due to above)
* You have 100% flexibility (1:N) of elements, styles and props
* Requirement comprehension cost is paid once and QA effort is (much?) lower

Bad things:

* You must be able to wrap all platforms apis (mostly easy, but sometimes complicated)
* Because the boundry between domain and presentation markup is so distinct, I left my Component files get messy (but simple). It would be a discipline of developers to always "pull out" atomic components for reuse.

#### Ongoing Questions

* How much confidence does the existing web unit/component tests give? 
* What is the DX of selenium tests for native apps?
* How to best organise multi-platform codebases?
* Would HAL be a better alternative than packager

#### Technology notes

###### ReactNative


Compared to React web developement:

* Shakey developer experience - a few strange problems with strange solutions, frequent crashes
* Does not use webpack, uses packager instead (watchman)
* Slightly more difficult to debug than web


###### Expo (& create-react-native-app)

Incredible.

IIRC the last time I setup a react-native project using a custom webpack config it took ~ 1.5 days until I was happy to develop on it. Expo setup took a few hours, was more stable, easier add features to. Probably the one of the coolest things, is I can send a QR code to my remote-QA and within seconds they can be looking at the app as it is on my computer.

###### styled-components

These harmonise well with the domain-markup-only you end up with in Option C

###### react-base

At the time of this writing, of all React Native UI frameworks, this has the best blend of expressive markup, realworkd components, docs, and community support. The theming method is quite a buy-in, and in general we will probably transition away in the medium term - but its a great starting point.
