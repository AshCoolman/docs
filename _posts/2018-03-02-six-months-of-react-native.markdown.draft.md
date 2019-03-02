---
layout: post
title:  "Six months of ReactNative"
description:  "Impressions, tips, etc"
date:   2018-03-02 20:32:12
categories: react-native
---

### Intro

#### What to expect

1. React Native:
  1. Is optimised for the common simple CRUD app
    1. File access is slow
  1. Android is slow
  1. The ReactNative are now doing a great job at:
    1. Community communication
    1. Project governance
    1. Building up to addressing some of the core architecture problems
1. iOS development:
  1. Xcode is pretty decent
  1. waiting for Test flight is slow
1. Android development:
  1. Android Studio & gradle feels shakey
  1. building/installing apks to simulators & real devices via CLI is awesome


#### Tips

1. The native-module ecosystem
  1. moves much slower than the javascript package ecosystem
  1. some third-party modules I assumed would be super-tight, suffer from a surpring about of quality variability
1. You can click on an error in the Simulator, to open the file in VSCode


#### Concepts

1. Role of Javascript:
  1. js serves as a scripting language to ReactNative (which actually does the work)
  1. js instructions are passed to ReactNative over an asynchronous bridge
1. The bridge:
  1. asynchronous bridge is like controlling a UI via a HTTP REST API, except:
    1. very low-latency
    1. very low-bandwidth
    And this can cause 
