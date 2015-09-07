
DOCS
====

Where?
------

We standardise on having documentation in the project.  
We have a README in the project root and then a directory in the root of the
project called docs/ and write the main documentation into files there.

On format, the most widely used is .rst, so we could go for that.  Or
for our internal purposes markdown could also be fine.  

What?
-----

What should we document?  Two Scoops of Django recommends:

- README.rst - at least a short intro and links to other things

- docs/deploy.rst - deploying to servers

- docs/install.rst - getting set up for development

- docs/architecture.rst - guide to the areas of the project

- docs/upgrade-guide.rst - add notes about things that will need to be
  done when package X needs to be upgraded.

- docs/deploy-notes.rst - add notes about things that need to be done
  on/after deploy of a particular version/feature.  I've had a number of
  times where there's a one off thing to be done, and the amount of work
  integrating that into deployment scripts seemed too much.

And then anything else as required by the project.

