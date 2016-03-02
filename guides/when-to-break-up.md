
* **Should I start spliting this file up?**
    * How many lines is it?
        *  80<? **Then** yes, if its stable
        *  ~80? **Then** yes, if there are low hanging fruit
        * 120+? **Then** yes
    * Is there a decent npmjs package you could?
        * Yes? **Then** yes
    * Is this code part of a more complicated system?
        * Yes? **Then** yes, you need well defined interfaces/APIs
* **Which strategy should I use?**
    * Is the code only going to be used in the current project?
        * Yes? Is the file less than 10 lines?
            * Yes? **Then** create a catch-all module for all helper/util functions/values
            * No? Is it single concern?
                * Yes? **Then** split it out reusable services, and leave in biz logic
                * No? **Then** take the time to split into different modules
        * No? Are other components relying on its API _right now_?
            * No? **Then** make a local package with `npm link` until it has stabilised
            * Yes? **Then** make a proper semver'd packge
