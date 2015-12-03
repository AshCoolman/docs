# Product and engineering web workflow

## Intro

Web applications are growing in scale, and there is allot of emphasis on breaking up functionality (modules, packages) and having well-tested, reusable components. There are also great UI frameworks that greatly increase speed - at the cost of investing in their design language (Material UI, Bootstrap, etc).

## Proposed workflow

### Roles

Actors:

* PRODUCT empowers STAKEHOLDER
* ENG empowers PRODUCT

Requirements > tech >  outcome:

* LOW-FIDELITY DESIGN informs COMPONENT LIBRARY, which provides functionality
* STYLEGUIDE informs THEME, which provides look & feel

### Flow

1. PRODUCT identifies the functionality they need to _compose_, and create a LOW-FIDELITY DESIGN
2. DEV provides COMPONENT LIBRARY (material, bootstrap etc) and data, that can _compose_ this functionality
3. DEV providers data stores, DEV & PRODUCT compose functionality from the COMPONENT LIBRARY
4. ALL test, at every step of the way

### Adding new components

Adding new components is extremely expensive. Thus the requirements to add a new component are as follows:

1. Its easy, or
2. there is no alternative, or
3. no higher priorities
