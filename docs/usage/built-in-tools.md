## Utility and stand-alone scripts

Some tools are included in the core to ease management and development of new packages.


Utility scripts allow to achieve some tasks that are not handled by the core, for instance checking the consistency of a new package.

Those act like some sort of plugins, written in PHP and located in the folder: `data/utils/`


Here is a complete list of the scripts that come out-of-the-box.


### Installation & Config utilities

### Package utilities

\- core validation
\- package validation: consistency checks between DB and class as well as syntax validation for
classes (PHP), views (HTML) and translation files (json)


\- Create a compatible database based on a SQL schema
\- Generate a PHP class from an existing table
\- Generate files for default views (list.default.html and form.default.html)
\- Data import / export

To consult the list of the plugins and apply them to one or more package, you may use the `core_utils` application (URL example: http://localhost/easyobject/index.php?show=core_utils).

