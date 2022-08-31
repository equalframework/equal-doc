## Config

The config parameters are present globally inside the `config/config.inc.php` file, but also can be overwritten (with the **namespace config**) inside packages with a `config.inc.php` file.

Every overwritten parameter is limited to the package it belongs to.

If they are used by a controller, they must be listed by it. If there is an error, displays `Error 500`.

The constants are checked by the announce() function inside `eq.lib.php`.