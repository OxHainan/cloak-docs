Call for Contributions
===========================

All Contributions Counts
----------------------------

We are always calling for talented, self-motivated developers, researchers or students
who are excited about our vision. 
For now, all code of Cloak has been published as four repositories:

- `Cloak Compiler <https://github.com/OxHainan/cloak-compiler>`_
- `Cloak Network <https://github.com/OxHainan/cloak-tee>`_
- `Cloak Client <https://github.com/OxHainan/cloak-client>`_
- `Cloak Documentation <https://github.com/OxHainan/cloak-docs>`_

There are plenty of options how you can contribute to Cloak. 
In particular, we appreciate support in the following areas:

* Reporting issues.
* Fixing and responding to Cloak's GitHub issues, especially those tagged as
  "good first issue"` which are meant as introductory issues for external contributors.
* Improving the documentation.

Please note that this project follows the `Contributor Code of Conduct <https://raw.githubusercontent.com/ethereum/solidity/develop/CODE_OF_CONDUCT.md>`_. 
By participating in this project - in the issues, pull requests, or Gitter channels - you agree to abide by its terms.


How to Report Issues
----------------------------


When reporting issues, please mention the following details:

* Cloak version.
* Source code (if applicable).
* Operating system.
* Steps to reproduce the issue.
* Actual vs. expected behaviour.

Reducing the source code that caused the issue to a bare minimum is always
very helpful and sometimes even clarifies a misunderstanding.

Workflow for Pull Requests
----------------------------


In order to contribute, please fork off of the ``develop`` branch and make your
changes there. Your commit messages should detail *why* you made your change
in addition to *what* you did (unless it is a tiny change).

If you need to pull in any changes from ``develop`` after making your fork (for
example, to resolve potential merge conflicts), please avoid using ``git merge``
and instead, ``git rebase`` your branch. This will help us review your change
more easily.

Additionally, if you are writing a new feature, please ensure you add appropriate
test cases.

Finally, please make sure you respect the `coding style
<https://github.com/ethereum/solidity/blob/develop/CODING_STYLE.md>`_
for this project. Also, even though we do CI testing, please test your code and
ensure that it builds locally before submitting a pull request.

Thank you for your help!

.. _documentation-style:

Documentation Style Guide
----------------------------

In the following section you find style recommendations specifically focusing on documentation
contributions to Solidity.

English Language
^^^^^^^^^^^^^^^^^^

Use English, with British English spelling preferred, unless using project or brand names. Try to reduce the usage of
local slang and references, making your language as clear to all readers as possible. Below are some references to help:

* `Simplified technical English <https://en.wikipedia.org/wiki/Simplified_Technical_English>`_
* `International English <https://en.wikipedia.org/wiki/International_English>`_
* `British English spelling <https://en.oxforddictionaries.com/spelling/british-and-spelling>`_


Title Case for Headings
^^^^^^^^^^^^^^^^^^^^^^^^^

Use `title case <https://titlecase.com>`_ for headings. This means capitalise all principal words in
titles, but not articles, conjunctions, and prepositions unless they start the
title.

For example, the following are all correct:

* Title Case for Headings.
* For Headings Use Title Case.
* Local and State Variable Names.
* Order of Layout.

Expand Contractions
^^^^^^^^^^^^^^^^^^^^^

Use expanded contractions for words, for example:

* "Do not" instead of "Don't".
* "Can not" instead of "Can't".

Active and Passive Voice
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Active voice is typically recommended for tutorial style documentation as it
helps the reader understand who or what is performing a task. However, as the
Cloak documentation is a mixture of tutorials and reference content, passive
voice is sometimes more applicable.

As a summary:

* Use passive voice for technical reference, for example language definition and internals of the Cloak Network.
* Use active voice when describing recommendations on how to apply an aspect of Cloak.

For example, the below is in passive voice as it specifies an aspect of Solidity:

  Functions can be declared ``pure`` in which case they promise not to read
  from or modify the state.

For example, the below is in active voice as it discusses an application of Solidity:

  When invoking the compiler, you can specify how to discover the first element
  of a path, and also path prefix remappings.

Common Terms
^^^^^^^^^^^^^^^^^^^

* "function parameters" and "return variables", not input and output parameters.
