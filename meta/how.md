How We Write Standards
======================

Why?

* others will know the apps etc better and be better able to support them
* investment in automation around standard apps (deploy scripts, puppet etc)
* easier to read/parse code written in standard way
* less time arguing about standards when pairing on a project
* make it cheaper to deliver projects
* long time support, know what's missing from an old project

Why not?

* ignore useful new technologies because they're not on our standards list

How to write them?

* Brainstorm for possible ideas (find existing standards we agree with ?)
* One or two people write up ideas as RFC, include critical questions to be resolved
* circulated for review - github pull request, comments and discussion
* agreed at Tech Forum? at least discussed
* versioned standards? each set of standards assigned a serial/version number; apps to declare which version of the standards they follow
* agreed at econsensus?

How to record them?

* keep list of comments on standard, so they can be considered for next revision
* github repo, written in markdown

Questions

* recommended or mandatory?
* be aware of costs of going outside standards - lack of automation, ongoing support costs, number of people who can reasonably debug/support code
* tooling? apti-lint? code compliance?
* publish our standards
    * standard apps recorded in dye cookiecutter
* examples:
    * https://github.com/thoughtbot/tguides
    * pep8 http://legacy.python.org/dev/peps/pep-0008/
    * https://en.wikipedia.org/wiki/Coding_conventions#External_links
* license - CC-BY, CC0 ?
