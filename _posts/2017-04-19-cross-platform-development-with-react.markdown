---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---

#### Intro

Every line of code is a liability, but not every line creates value. I have been experimenting with [React Native](https://facebook.github.io/react-native/) to reuse domain code written for the web. Some large established teams have reported code reuse of 70%-90%, plus a bunch of other benefits to their output. I've recreated two containers, and in the process of getting them QA'd. The short version is, it was way faster, but many problems had to be overcome, and I'm still closingin on the optimum methodologies to use for low effort reuse.

#### Background

My team's priority is to build quailty, fast. Only if we can get an iOS/Android app relatively cheap- we would seriously consider it. Currently our web app is under development:

* 2 months of coding (~12k LOC)
* ~80% test coverage
* limited mobile designs

The web app uses the following technologies:

* react-router
* redux
* styled-components
* react-bootstrap

#### Cross platform development in React

Theoretically all domain code can be shared beween all platforms. A button that triggers a login process is semantically the same on Web and on iOS. But how that button is rendered, and the APIs available when the button is pressed/clicked are platform-specific.

React components build a "virtual-DOM", which is then mapped to platform elements. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious platform targets, but proof of the virtual-DOM decoupling can be seen in other mappings e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 

So we have established the following _conceptual logic_ must appear in cross platform code:

```
if platform === 'web' then
  render('<button onClick="doLogin">Login</button>')
else if platform === 'native-app' then
  render('<TouchableHighlight />');
endif
```

But practically speaking, where is the best place to put this?

<sup> * react-native registers the components with the native system, which actually bundles and runs the app. [Expo](https://expo.io/) takes care of the iOS vs Android mapping.</sup>

###### Option A: map 1 native to 1 web


Projects like [react-native-web](https://github.com/necolas/react-native-web) rebuild the [react-native elements & API](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web. 


######

 _React-native-web_ itself is particularily powerful and mature, and really lets you write for native, and get web almost for free. *Including translating ReactNative styles/Apis into web styles/Apis!


In the cases where its limitations are acceptable, there is nothing more elegant. As I see it, these are the libraries limitates (which are by design)

* limited semantic elements (e.g. no checkbox)
* limited styling (e.g. no fixed positioning)
* no clear development path to take web design to native
* not compatible with react-native ui frameworks

The project basically [self identifies as](https://github.com/KodersLab/react-native-for-web#why-use-react-native-for-web) a solution when you just need something working, on a budget and are happy with the [limited palette](https://necolas.github.io/react-native-web/storybook/?selectedKind=APIs&selectedStory=Clipboard&full=0&down=1&left=1&panelRight=0&downPanel=kadirahq%2Fstorybook-addon-actions%2Factions-panel).

I think these limitations also extend to any sort of technique that maps elements across platforms 1:1 - including decorators, HOC, etc

###### Option B: platform switching expressions in render logic

Using expressions to render different markup per platform is possible. But I think it is quite obvious that arbitrary if/elses in a render function would quickly get messy.

```
createElement(platform.web ? 'span' : 'Text', 'hello');

const Zone = props =>
  platform.web
  ? <div onClick={props.onClick} {...props}/>
  : <TouchableHighlight onPress={props.onClick}>
      <View {...props}
    </TouchableHighlight>

```

_Platform detecting expressions_ introduce controlflow all over your code base. But the platform never changes at run time, so logically this is pure cost without benefit. Or to put it another way - platform is not a variable, so lets not treat it as such.

###### Option C: Use domain components parametised by platform

React components are functions with state, that build virtualDOM (domain), which maps to elements (platform). We can use functions to create React components _parametised_ with platform elements. Thus we essentially need a [function to create a function](https://www.ibm.com/developerworks/library/j-ft10/) (depending on your programming-heritage, you may want to rename `<component>Factory` with [`<component>Partial`](https://medium.com/functional-javascript/higher-order-functions-78084829fff4) in the example below)


Lets start off with something analgous and work up to this:

If I have a `Link` component and I want to "decouple" style from the component. One way is to use `react-css-modules`:

```jsx
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

Now back to the cross-platform problem:

We want to decouple the elements (and the style, and the props).

Part 1: The platform agnostic domain code:

```jsx
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

So now we have a function, that returns new Link components when you call it. Please take care to note:

* No platform elements are specified (e.g. div)
* No platform apis are specified (e.g. window.alert)
* No styling in component
* Component names are entirely 

Part 2: Create the Link, with browser elements:

NOTE: we have to abandon css-modules, because 

```jsx
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
* `doSay` has the implementation passed in, but not the usage
* WARNING: Each component with a _style attribute_ needs css() called on it (one in this example)

Part 3: Create the Link, with React Native elements

```jsx
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


```jsx
// Link.native.js
import LinkFactory from './LinkFactory';
import { View as Wrapper, Button as SayButton } from '../native/components';
import { alert as doSay } from '../native/apis';
export default LinkFactory({ Wrapper, Hat, SayButton, doSay });
```

```jsx
// Link.web.js
import LinkFactory from './LinkFactory';
import { View as Wrapper, Button as SayButton } from '../web/components';
import { alert as doSay } from '../web/apis';
export default LinkFactory({ Wrapper, Hat, SayButton, doSay });
```

**Discussion point** 

`LinkFactory` is not by a strict intepretation a _Higher order component_ :

> A higher-order component is a function that takes a component and returns a new component.

It doesn't take a Component as an input, it takes a has of components - and uses them in another component. But regardless, it is a [higher order function](http://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK)

> Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions.


Do you want to know the developer experience on converting things large nested container/components? Well skip to here

Ok, on with a slightly more realistic example - a login component:

```js
import api from 'api';

function LoginFactory({Wrapper, Header, Button})  {
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

```jsx
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

```jsx
import { View, Text, TouchableOpacity as Touch} from 'react-native';

const Login = LoginFactory({
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>
});
AppRegistry.registerComponent(<Login />);
```

### What did I do exactly for the prototype

I converted two large React container components (and ~23 stateless components) from the web project. I relied only on the technique described in option C, and tried to do an close-approximation of the web design converted to native. Both features used a subset of "[atomic](http://bradfrost.com/blog/post/atomic-web-design/#atoms)" components (Buttons, Headings, Icons, ErrorText etc).

The first feature used 1 container and 1 signifigant "[molecule](http://bradfrost.com/blog/post/atomic-web-design/#molecules)" and had an infinite-scroll behavior.

The second feature was a huge pain to build for web, as it had 2 containers, but ~14 signifigant stateless components. All three of the frontend developers on my team had worked on this. Signifigant features included a "read more" button that would only appear if the text was long enough, markdown rendering, and a heap of custom atoms for lists etc. 

### What was it like doing this for real


The first feature was very easy - including the _equivalent_ scrolling logic for Native (which took ~1.5hrs).

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

IIRC the last time I setup a react-native project using a custom webpack config it took ~1.5 days until I was happy to develop on it. Expo setup took a few hours, was more stable, easier add features to. Probably the one of the coolest things, is I can send a QR code to my remote-QA and within seconds they can be looking at the app as it is on my computer.

###### styled-components

These harmonise well with the domain-markup-only you end up with in Option C

###### react-base

At the time of this writing, of all React Native UI frameworks, this has the best blend of expressive markup, realworkd components, docs, and community support. The theming method is quite a buy-in, and in general we will probably transition away in the medium term - but its a great starting point.
