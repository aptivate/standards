Python and Django
=================

Existing Standards
------------------

In general we follow [PEP 8](http://www.python.org/dev/peps/pep-0008) except where
specified below.

The [Django Coding Standards](https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/coding-style/)
also seem generally sensible but we haven't explicitly agreed to them.

Exceptions to PEP8
------------------

### Long Lines

Lines over 80 chars are acceptable to improve readability.

* Perhaps go to 90 if necessary to avoid wrapping?
* if you have very long test names maybe move some description to docstrings

### Backslash for line continuation

Don't ban backslashes for line continuation (but prefer parentheses).

### Where do import statements go

`import` statements should be at the top of the file unless there is a good
reason not to.  Ultimately that is a matter of judgement, but good reasons
to import next to where code is used include:

* avoiding imports as soon as module is imported (eg want to be able to import settings.py without importing all of Django, but there is a function in settings.py which when run requires Django).
* module to be imported is optional and only used when config says so

### Relative Imports

Prefer relative imports within a reusable module to make renaming easier

### Whitespace

Using whitespace to align assignments, etc. is allowed

Inline comments don't have to be separated by at least two spaces from the statement.

Other Standards and Conventions
-------------------------------

### Grouping Imports

+ grouping imports; (stdlib, django, third party, own), alphabetically within groups (pep8 says we "should" do this anyway) - in alphabetical order
-- this would mean using pydev/rope to autoimport itself is not enough, they need to be manually put in this format to improve readability.

### Class definitions

Put metaclasses first in class definition (Django)

### Future Imports

Always (unless there is a good reason) use

    from __future__ import unicode_literals, absolute_imports

### String quoting

Prefer strings quoted with " and identifiers with '

By *strings* we mean strings that might be seen by users of your code, eg.
`"Hello 'foo' bar"`.  You might also want to default to doing i18n of these strings
`_("Hello 'foo' bar")`.

By *identifiers* we mean strings that are only used internally, such as
short options names in databases.  eg. 'foo_field'

### Whitespace

* a blank line at top of classes, after the class definition, is optional - don't get into edit wars over it
* indentation on empty lines is verboten
* no trailing whitespace after lines with content

Other thoughts
--------------

Mixing from and import statements can look ugly.

The Clean Code book has many good recommendations.

Settings to support standards
-----------------------------

### Vim

To be done.

### Eclipse

To be done.

### Git Hooks

To be done.

### Jenkins config

To be done.
