# Introduction   
## Case for mid-level understanding
Writing Protractor tests can be deceptively simple. You can jump into writing tests using the syntax from [Protractor tutorial](http://angular.github.io/protractor/#/tutorial) but *eventually* you will need a deeper understanding.

This article will bridge the gap between your first tests and the deeper understanding contained in the [Protractor docs](http://angular.github.io/protractor/#/toc)

## Prerequisites:
These four concepts are the pre-requisites to writing good protractor tests. They are briefly listed here, but also inserted with more detail further down. I encourage you to stop at each "_research point_" below, and have a little read.

1. Jasmine
1. Promises
1. Webdriver control flow
1. Protractor auto-waits for events to finish
 

# Background: Tech and infrastructure

## Technology

### Jasmine

Your test instructions are written using  Jasmine. Jasmine is a Javascript testing framework made from:

1. a set of principles / code to help you organise your tests (`describe`, `it`, `beforeEach`, `afterEach`, etc)
1. code to help write expressive tests easily: (`expect(bradPit.eating).toEqual(true)`)

**Research point 1 of 4: Jasmine**
[Jasmine docs: read for 10 minutes to get the idea](http://jasmine.github.io/2.0/introduction.html)

## Tech: Writing tests for the browser

Writing tests for a browser has two extra complications, which the remaining technology solves:

1. controlling the browser
1. understanding what (asynchronous) actions have happened in the browser 

### Selenium ( + Webdriver )

Selenium was originally a testing framework that ran javascript in-browser. But in-browser javascript is nerfed to limit the power of malicous code. The Webdriver app was created that mimics a human user, and so has the same security sandbox as a real person.

Webdriver has a completely asynchronous API, relying on the language feature *promises*. 

**Research point 2 of 4: Promises**
If you don't have an understanding of promises, you will need to sink some time here:

A quick google:

1. [this reasonably concise explanation](http://12devs.co.uk/articles/promises-an-alternative-way-to-approach-asynchronous-javascript/) by Kishore Nallan
1. [this jQuery based explanation](http://davidwalsh.name/write-javascript-promises) by Landon Schropp.


### Protractor

Node.js app that:

1. wraps webdriverjs (which is a js wrapper for the Webdriver app API)
1. adapts Jasmine to work better with webdriverjs

**Research point 3 of 4: Webdriver control flow**
[Then read this section of the Webdriver docs on promises](https://code.google.com/p/selenium/wiki/WebDriverJs#Understanding_the_API)

**Research point 4 of 4: Protractor auto-waits for events to finish**
[Read this description of the control flow queue in protractor](http://angular.github.io/protractor/#/control-flow)


# Example tests

## Test explanation

**Note: these are in coffeescript**

### Webpage

Lets assume I have a checkbox input field that acts as a "select all" for something else. It has two states:

1. checked:
```html
<input type="checked" ng-model="selectAll" checked="checked">
```

2. unchecked
```html
<input type="checked" ng-model="selectAll">
```

### Test

Lets assume the input begins checked. My test will:
1. click the input
1. evaluate if the input has the html attribute "checked" removed


## Test: No protractor magic

Lets write a test as if we were familiar with Jasmine, but not with Protractor's magic:

```coffee
describe "Checking selectall, ", ->
  it "exists,", ->
    selectAll = element By.css 'input[ng-model=selectAll]'
    browser.wait (-> browser.isElementPresent selectAll), 500
    expect browser.isElementPresent selectAll
      .toEqual true

    describe "can be toggled off...", ->
      it "toggles once", (done)->
        selectAll.click().then ->
          expect selectAll.getAttribute("checked")
            .not.toEqual "true"
          done()
```

I should emphasies _this works fine_, but it is unnessecary wordy and indenty:

1. `browser.wait()` 
1. The optional `done` callback variable of `it()`
1. Setting the promise's `then()` function


## Test: using Protractor's magic

Lets take the above code, and use Protractor's auto-wait feature:

```coffee
describe "Checking selectall, ", ->
  it "exists,", ->
    selectAll = element By.css 'input[ng-model=selectAll]'
    expect browser.isElementPresent selectAll
      .toEqual true

    describe "can be toggled off, on, and off.", ->
      it "clicks selectall once", ->
        selectAll.click()
        expect selectAll.getAttribute("checked")
          .not.toEqual "true"
```

All the code dealing with asynchronity is gone, making it more straight-forward.

**Danger**: Notice I don't test the attribute *checked* against the string "checked" as it appears in the html (or DOM node). Instead Protractor treats `checked = "checked"` as `checked = true`.

## Going back to promises

Ok lets try to console.log the input's `checked` attribute:

```coffee
describe "Checking selectall, ", ->
  it "exists,", ->
    selectAll = element By.css 'input[ng-model=selectAll]'
    expect browser.isElementPresent selectAll
      .toEqual true

    describe "can be toggled off, on, and off.", ->
      it "clicks selectall once", ->
        selectAll.click()
      
        #Show the attribute
        console.log "selectall.checked:", selectAll.getAttribute("checked" 
        expect selectAll.getAttribute("checked")
          .not.toEqual "true"


# OUTPUT:
# selectall.checked:, { parentElementArrayFinder:
#    { getWebElements: [Function],
#      actionResults_:
#       { then: [Function: then],
#         cancel: [Function: cancel],
#         isPending: [Function: isPending] },
#      locator_: { using: 'css selector', value: 'input[ng-model=selectAll]' },
#      click: [Function],
#      sendKeys: [Function],
#      getTagName: [Function],
#      getCssValue: [Function],
#      getAttribute: [Function],
#      getText: [Function],
#      getSize: [Function],
#      getLocation: [Function],
#       ... etc ...
```

If the output surprises you, let me re-visit the protractor docs on [control flow](http://angular.github.io/protractor/#/control-flow). In particular:

>WebDriverJS (and thus, Protractor) APIs are entirely asynchronous. All functions return promises.

The function returns a promise, so we need to wait until the promise has resolved to output it. Which we can do using `then(cb)`

```coffee
describe "Checking selectall, ", ->
  it "exists,", ->
    selectAll = element By.css 'input[ng-model=selectAll]'
    expect browser.isElementPresent selectAll
      .toEqual true

    describe "can be toggled off, on, and off.", ->
      it "clicks selectall once", ->
        selectAll.click()
      
        #Show the attribute
        selectAll.getAttribute("checked")
          .then (data)-> console.log "selectall.checked", data
        expect selectAll.getAttribute("checked")
          .not.toEqual "true"
# OUTPUT:
# selectall.checked: true
```

# Conclusion

So there you have it, an example of a protractor test. And some orientation around Jasmine, Protractor and Selenium.

# More

* [Good looking tutorial](https://github.com/Droogans/ProtractorPageObjects)
* [Important distinction between element.all vs driver.findElements](https://github.com/angular/protractor/issues/1008) and [when you may use driver.findElements](http://stackoverflow.com/questions/23863563/find-all-visible-elements-using-protractor)

_Disclaimer: originally written Dec 2014_
