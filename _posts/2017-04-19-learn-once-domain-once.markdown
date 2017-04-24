---
layout: post
title:  "Learn once, write domain once, write presentation anywhere"
description:  "My experiences with ReactNative have been very, VERY good"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---

#### Intro

One of the React mantras is:

> Learn once, write anywhere

This is really the "worst case" scenario - we can reuse code between React and React Native if we organise things carefully.

> Learn once, write domain once, write presentation anywhere

Every line also pushes a product forward; but it is also liability, and must be tested and maintained. Using ReactNative, some large established teams have reported code reuse of 70-90% between web and mobile apps. I've been looking at the realities of using ReactNative for my company which has only 4 frontend developers.


Our priority is to build our web offering fast. But if we can get an iOS app relatively cheap using React Native we would consider it. Currently we have:

* 2 months worth of development on a web-app (~12k LOC)
* ~3-4 devs
* 78% test coverage (increasingly TDD)
* Tech: Expo (formally exponent), create-react-app, react-router, redux, styled-components, and react-bootstrap (phasing-out).

##### Cross platform development

Theoretically we can share all domain code between all platforms. All platforms may have a button that triggers a Login process. But how that button is rendered, and the APIs available are platform-specific.

React components build a "virtual-DOM", which is then mapped to platform elements. [Web](https://www.npmjs.com/package/react-dom) and [native](http://facebook.github.io/react-native) are the obvious targets, but the concept of cirtual DOM is fleshed ut that there is also mappings for the terminal, VR, or 3d. 

Note: react-native actually registers the components with the native system that bundles and runs app. Expo further helps the ios vs android mapping.


##### The Cross-platform component

Conceptually, the platform must be chosen

```

if platform === 'web' then
  render('<button onClick="doLogin">Login</button>')
else if platform === 'native-app' then
  render('<TouchableHighlight />');
endif
```


##### Option A: 1:1 map native to web

https://github.com/necolas/react-native-web
This project has rebuilt all the [react-native elements](https://github.com/necolas/react-native-web/blob/master/src/index.js#L52) and its APIs for the web. It is an amazing peice of tech that lets you write for native and get for web - but this has some inbuilt limitations which must be considered carefully:

* Limited semantic elements (e.g. no checkbox)
* Limited styling (e.g. no fixed positioning)
* no clear development path to take web design to native
* no native ui framework to web

Anything outside the the 1:1 mapping of elements would need to be overriden with specific markup anyway. Practically speaking this is not what the library is designed for, and may end up as a "framework fight". Secondly, and potentially more gravely, this would break the semantic mapping between platforms.

In the cases where these limitations are acceptable, there is nothing more elegant.

##### Option B: Platform switching expressions in domain code

Regardless of if we used Option A, using logic in our code the created different markup per platform could be an option. I think it is quite obvious having conceptual if/elses that handle platform rendering within the rendering logic would quickly get messy and can be discounted out of hand.


##### Option C: Domain:platform mapping

To recap, React components functions with state, that build virtual DOM (domain), which maps to elements (platform). We can use functions that produce Components that deal with the same logic/virtualDOM, but are _parametised_ with platform elements:


```
import api from 'api';

function LoginFactory({Wrapper, Header, Button}) 
  class Login extends React.Component {
    constructor() {
      super();
    }
    handleLogin = () => {
      api
        .post(this.context.loginDetails)
        .then(this.context.router.push('/home'))
        .catch(err=>this.setState( ()=> { err }))
    }
    render() {
      <Wrapper>
        <Header>Press to login</Header>
        <Button onClick={handleLogin}>{err || 'Login'}</Button>
      </Wrapper>
    }
  }
  return Login; // Factory returns newly parametised component
}
```

Web:

```
const Login = LoginFactory({
  Wrapper: p=><div {...p} />,
  Header:p=><h1 {...p} />,
  Button: p=><button {...p} />)
})

ReactDOM.render(<Login />);
```

Native:

```
import { View, Text, TouchableOpacity as Touch} from 'react-native';

const Login = LoginFactory(
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>
);
AppRegistry.register(<Login />);
```

Major beneifts of this approach:

* Flexibility of what is rendered
  - In practice, one semantic tag can require multiple elements
  - Not limited to per-platforms, could be used for split-testing, or incremental improvments
* Component markup is left purely domain focused

#### Impressions

I converted two large containers, with (~23 stateless components) each:

* In raw lines of javascript, adding a native components adds ~10-40% more code
* Of the time taken to produce a web feature, it takes a further +5-50% extra to build in react-native

Some qualitive observations:

* Same developer for all platforms means requirements understood one
* Lots of QA reuse, unsure exactly how much
* Fun work
* React Native has some unstable aspects

#### Ongoing Questions

* How much confidence does the existing web unit/component tests give? 
* DX selenium tests
* How to best organise multiple-platform codebase

#### Technology notes

##### Expo (& create-react-native-app)

A big time saver. IIRC the last time I setup a react-native project using a custom webpack config it took ~2.5 days, and with constant problems. Expo setup took a few hours, was more stable, and much easier to extend with custom features.

###### styled-components

This approach harmonises well with the domain-markup-only components end up with.

###### react-base

At the time of this writing, of all React Native UI frameworks, this has the best blend of expressive markup, realworkd components, docs, and community support. The theming method is quite a buy-in, and in general we will probably transition away in the medium term - but its a great starting point.
