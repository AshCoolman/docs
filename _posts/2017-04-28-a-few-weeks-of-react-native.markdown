---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-28 9:53:04
categories: react, react-native, ios, android
---


# Using React Native

Best thing: requirement-comprehension-cost is paid once and QA effort is (much?) lower

Worst thing: Shaky developer experience

Compared to React web developement:

* A few strange problems with strange solutions, frequent crashes
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
