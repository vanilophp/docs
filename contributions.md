# Contribution Guide

## Bug Reports

Bug reports are welcome via Github issues.

If you file a bug report, please always enter a descriptive title and a
clear description of the problem. You should also include as much relevant
information as possible and a code sample that demonstrates the issue.
The goal of a bug report is to make it easy for yourself - and others -
to replicate the bug and develop a fix.

Remember, bug reports are created in the hope that others with the same
problem will be able to collaborate with you on solving it. Do not
expect that the bug report will automatically see any activity or that
others will jump to fix it. Creating a bug report serves to help
yourself and others start on the path of fixing the problem.

The Vanilo source code is managed on GitHub, and there are repositories
for each of the separate components:

- [Framework](https://github.com/vanilophp/framework) (glue between components) <== use this if unsure
- [Address](https://github.com/vanilophp/address)
- [Product](https://github.com/vanilophp/product)
- [Cart](https://github.com/vanilophp/cart)
- [Checkout](https://github.com/vanilophp/checkout)
- [Order](https://github.com/vanilophp/checkout)
- [Documentation](https://github.com/vanilophp/docs)

## Features, Improvements

You may propose new features or improvements in the [Vanilo Framework
issue board](https://github.com/vanilophp/framework/issues). If you
propose a new feature, please be willing to implement at least some of
the code that would be needed to complete the feature.

## Pull Requests

As of v0.1, please submit all PRs to the `master` branch only.

This will change as of v0.2.

### Coding Style

Vanilo follows the PSR-2 coding standard and the PSR-4 autoloading standard.

### Tests

Please note that the test suite needs to pass in order to merge your
pull request. If you've added new features you should add their test
cases as well. If you've made a BC and it breaks tests (it should) then
the tests have to be modified accordingly (in case the BC was agreed upon)
