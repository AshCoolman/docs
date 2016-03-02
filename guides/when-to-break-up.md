
1. **Should I start spliting this file up?**
    1. How many lines is it?
        1.  80<? **Then** yes, if its stable
        1.  ~80? **Then** yes, if there are low hanging fruit
        1. 120+? **Then** yes
    1. Is there a decent npmjs package you could?
        1. Yes? **Then** yes
    1. Is this code part of a more complicated system?
        1. Yes? **Then** yes, you need well defined interfaces/APIs
1. **Which strategy should I use?**
    1. Is the code only going to be used in the current project?
        1. Yes? Is the file less than 10 lines?
            1. Yes? **Then** create a catch-all module for all helper/util functions/values
            1. No? Is it single concern?
                1. Yes? **Then** split it out reusable services, and leave in biz logic
                1. No? **Then** take the time to split into different modules
        1. No? Are other components relying on its API _right now_?
            1. No? **Then** make a local package with `npm link` until it has stabilised
            1. Yes? **Then** make a proper semver'd packge
