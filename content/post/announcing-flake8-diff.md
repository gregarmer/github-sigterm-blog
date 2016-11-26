+++
date        = "2015-01-06T18:50:00-04:00"
title       = "Announcing flake8-diff"
description = ""
tags        = [ "software engineering", "code", "python" ]
topics      = [ "code" ]
slug        = "announcing-flake8-diff"
+++

### Purpose

This utility allows you to run flake8 over a set of changed files and filter out violations that would be introduced by merging those changes.

We use this as part of our build / CI infrastructure to alert developers opening pull requests to new violations their pull request will introduce, if it were merged.

<!--more-->

### Using flake8-diff

```console
$ pip install flake8-diff
$ flake8-diff -h
usage: flake8-diff [-h] [--flake8-options ...] [--vcs {git}]
                   [--standard-flake8-output] [-v]
                   [--color {off,colorful,light,nocolor,dark,boring}]
                   [commit [commit ...]]

This script runs flake8 across a set of changed files and filters out
violations occurring only on the lines that were changed.

positional arguments:
  commit                At most two commit hashes or branch names which will
                        be compared to figure out changed lines between the
                        two. If only one commit is provided, that commit will
                        be compared against current files.Default is
                        "origin/master".

optional arguments:
  -h, --help            show this help message and exit
  --flake8-options ...  Options to be passed to flake8 command. Can be used to
                        configure flake8 on-the-fly when flake8 configuration
                        file is not present.
  --vcs {git}           VCS to use. By default VCS is attempted to determine
                        automatically. Can be any of "git"
  --standard-flake8-output
                        Output standard flake8 output instead of simplified,
                        more readable summary.
  -v, --verbose         Be verbose. This will print out every compared file.
                        Can be supplied multiple times to increase verbosity
                        level
  --color {off,colorful,light,nocolor,dark,boring}
                        Color theme to use. Default is "colorful". Can be any
                        of "off, colorful, light, nocolor, dark, boring"

```

### Contributing

Contributions are welcome, and they are greatly appreciated! Every little bit helps, and credit will always be given.

You can contribute in many ways:

#### Types of Contributions

##### Report Bugs

Report bugs at https://github.com/dealertrack/flake8-diff/issues.

If you are reporting a bug, please include:

* Any details about your local setup that might be helpful in troubleshooting.
* Detailed steps to reproduce the bug.

##### Fix Bugs

Look through the GitHub issues for bugs. Anything tagged with "bug" is open to whoever wants to fix it.

##### Implement Features

Look through the GitHub issues for features. Anything tagged with "enhancement" is open to whoever wants to implement it.

##### Write Documentation

flake8-diff could always use more documentation, whether as part of the official docs, in docstrings, or even on the web in blog posts, articles, and such.

##### Submit Feedback

The best way to send feedback is to file an issue at https://github.com/dealertrack/flake8-diff/issues.

If you are proposing a feature:

* Explain in detail how it would work.
* Keep the scope as narrow as possible, to make it easier to implement.
* Remember that this is a volunteer-driven project, and that contributions are welcome :)

#### Getting Started!

Ready to contribute? Here's how to set up `flake8-diff` for local development.

1. Fork the `flake8-diff` repo on GitHub.
2. Clone your fork locally:

    ```console
    $ git clone git@github.com:your_name_here/flake8-diff.git
    ```

3. Install your local copy into a virtualenv. Assuming you have virtualenvwrapper installed, this is how you set up your fork for local development:

    ```console
    $ mkvirtualenv flake8-diff
    $ cd flake8-diff/
    $ python setup.py develop
    ```

4. Create a branch for local development, then you can make your changes locally:

    ```console
    $ git checkout -b name-of-your-bugfix-or-feature
    ```

5. When you're done making changes, check that your changes pass flake8 and the tests, including testing other Python versions with tox:

    ```console
    $ flake8 flake8-diff tests
    $ python setup.py test
    $ tox
    ```
   To get flake8 and tox, just pip install them into your virtualenv.

6. Commit your changes and push your branch to GitHub:

    ```console
    $ git add .
    $ git commit -m "Your detailed description of your changes."
    $ git push origin name-of-your-bugfix-or-feature
    ```

7. Submit a pull request through the GitHub website.

#### Pull Request Guidelines

Before you submit a pull request, check that it meets these guidelines:

1. The pull request should include tests.
2. If the pull request adds functionality, the docs should be updated. Put your new functionality into a function with a docstring, and add the feature to the list in README.md.
3. The pull request should work for Python 2.6, 2.7, and 3.3, and for PyPy. Check https://travis-ci.org/dealertrack/flake8-diff/pull_requests and make sure that the tests pass for all supported Python versions.
