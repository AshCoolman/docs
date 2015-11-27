# When to break up

Decision tree when considering splitting up your a file into smaller ones

* Will you ever use this in another project?
  * Yes: Are there any natural foreseeable dependencies shared with the current project?
    * No: Is it a method or less?
      * No: _Package it_ probably
      * Yes: Will you use it often?
        * Yes: Is the equivalent on npmjs?
          * Yes: _Use npmjs package_
          * No:  _Package it_
        * No: _Modulise it for now_
    * Yes: Will this module end up more than 300 LOC
      * Yes: Can you be bothered versioning it right now?
        * Yes: Can you be bothered maintaining the dependency semver in dependent projects?
          * Yes: _Package it_
          * No: _Package it with npm link_
        * No: _Modulise it, until warrents packagng_
      * No: _Modulise it_
  * No: Is it over 80 lines?
    * No: Is it a single concern
      * Yes: _Leave it_
      * No: Are the division of concern clear to you now?
        * No: Keep thinking about it as you work on it, then try to split it after
        * Yes: Split the file
    * Yes: Is it over 120 lines?
      * Yes: Split the file into multiple files
