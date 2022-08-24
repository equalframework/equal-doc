# Contributing

New contributions to eQual are welcome, but we ask that you please follow these guidelines:

* Before opening a PR for additions or changes, please discuss those by filing an issue on [GitHub](https://github.com/cedricfrancoys/equal/issues) or asking about it on [Discord](https://discord.gg/BNCPYxD9kk) (#general channel). This will save you development time by getting feedback upfront and make review faster by giving the maintainers more context and details.

* Check that your package will pass the consistency tests (`$ ./equal.run --do=test_package-consistency`).

* Add unit tests (in the `packages/{your_package}/tests/`) and descriptions to the new classes and controllers .

* Avoid breaking changes unless there is an upcoming major release, which is infrequent. We encourage people to write distinct libraries and/or packages for most new advanced features, and care a lot about backwards compatibility.

  

## Joining the project
Active committers and contributors are invited to introduce themselves and request commit access to the project on the Discord [#join](https://discord.gg/65WcBQFVg6) channel. If you think you can help, we'd love to have you!



## Bugs and Issues
Please report these on our [GitHub page](https://github.com/cedricfrancoys/equal/issues) . Please do not use issues for support requests: for help using eQual, please consider Stack Overflow.

Well structured, detailed bug reports are hugely valuable for the project.

Guidelines for reporting bugs:

* Check the issue search to see if it has already been reported
* Isolate the problem to a simple test case
* Please include a code snippet that demonstrates the bug. If filing a bug against master, you may reference the latest code using the URL of the repository of the branch (e.g. `https://github.com/cedricfrancoys/equal/blob/master/eq.lib.php` - changing the filename to point at the file you need as appropriate). 
* Please provide any additional details associated with the bug, if it's generic or only happens with a certain configuration or data set.