# Contribution Guide



## Submitting a package

Guidelines for submitting a package:

* Check that your package will pass the consistency tests (`$ ./equal.run --do=test_package-consistency`).

* Make sure your package comes with unit tests (in the `packages/{your_package}/tests/`) and that classes and controllers have descriptions and helpers.

    

## Submitting eQual core contributions

Guidelines for eQual core contributions : 

* All new development should be on feature/fix branches, which are then merged to the `master` branch once stable and approved; so the `master` branch is always the most up-to-date, working code

* Avoid breaking changes unless there is an upcoming major release, which is infrequent. We encourage people to write distinct libraries and/or packages for most new advanced features, and care a lot about backwards compatibility.

    


## Unit Tests

When writing Unit Tests, please:

* Always try to write Unit Tests for both the happy and unhappy scenarios.
* Put all assertions in the Test itself, not in external classes or functions (even if this means code duplication between tests).
* Always try to split the test logic into AAA callbacks `arrange()`, `act()`, `assert()` and `rollback()` for each Test.
* If you change any global settings, make sure that you reset to the default in the `rollback()`.
* Don't over-complicate test code by testing several behaviors in the same test.

This makes it easier to see exactly what is being tested when reviewing the PR. I want to be able to see it in the PR, not have to hunt in other unchanged classes to see what the test is doing.



## Naming conventions

The languages involved in eQual are varied: JS, TS, PHP, JSON, HTML, whether it's at the back-end or front-end. 

To facilitate cross-stack work, communication between teams, and code reading, the same logic is used for the syntax for variables, constants, classes, functions, and methods.

Indeed, consistently following naming conventions throughout the codebase of packages enhances readability, maintainability, and collaboration among developers.

Here is a summary of the conventions used across the framework:

**1. Scalar Variables & Functions - snake_case**

   - Scalar variables and functions (or var holding an anonymous function) should be named using snake_case, where words are separated by underscores.
     Examples: `$total_count`, `calculate_area()`, `calculate_area()`

**2. Objects - camelCase**

   - Objects (vars holding instances of classes) should be named using camelCase, starting with a lowercase letter and capitalizing subsequent words.
     Examples: `$myObject`, `$userProfile`

**3. Classes - PascalCase**

   - Classes should be named using PascalCase, where each word in the name is capitalized, including the first word.
     Example: `BankAccount`, `UserProfileManager`

**4. Classes Members - camelCase**

   - Class members, including attributes and methods, should follow camelCase convention.
     Example: `$this->myAttribute`, `calculateInterest()`

**5. Entity Properties - snake_case**

   - Entity properties (as defined in `getColumns()` for classes or `params` for controllers), should use snake_case.
     Example: `first_name`, `is_paid`
   - Maps and Objects properties should be named using snake_case
      Example: `{origin_country: "BE", is_shipped: true} `

**6. Controllers - kebab-case**

   - Controllers, typically used in web development frameworks, should be named using kebab-case, where words are separated by hyphens.
     Example: `user-controller.php`, `do-pay.php`



## Commit convention

As commit convention, we adopt Conventional Commits v1.0.0. We have a history of commits that do not assume the convention, but any new commit should follow it.
The commit message should be structured as follows:

```
<type>[(scope)]: <description>

[body]

[footer(s)]
```
(items within square brackets are optional)
[See examples here](https://www.conventionalcommits.org/en/v1.0.0/#examples)

The most important elements in the commit message to communicating the intent to the consumers are the type and the description

### type
* `fix:` type when patching a bug
* `feat:` type when introducing a new feature

Other commonly used types : `build:`, `docs:`, `style:`, `refactor:`, `test:`
Appending `!` after the type means that a breaking change was introduced.

### description
All messages should describe the action performed by the author of the commit (what has been done : "added", "removed", "improved", "corrected").

### examples
```
feat: allowed config object to extend other configs
```
```
feat(api)!: added sending of an email to the customer when a product is shipped
```
```
docs: improved comments
```
```
docs: corrected spelling in CHANGELOG
```



## Coding conventions


Coding conventions guidelines:

* eQual naming conventions should be used;
* reference guidelines of the code language used should be followed;
* significant functionality should come with appropriate testing to be run in the automated test suite;
* licensing guidelines must be followed.



## Licensing Guidelines

The following guidelines apply to all repositories under the eQual organization:

* our Governance's licensing requirements must be respected;
* unless otherwise specified, all code must be licensed under the GNU GPL 3.0;
* each repository must have a `LICENSE` file in its root folder and, when applicable, a `3rdpartylicenses.txt` file;
* if third-party code is used, their licenses must be vetted to ensure compatibility with our licensing requirements.