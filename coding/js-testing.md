Javascript testing
------------------

## Installation

### django.js

Install djangojs (pip_packages.txt):

    django.js==0.8.1

Add to THIRD_PARTY_APPS in `settings.py`.

Add the below to the global `urls.py`:

    if settings.ASSETS_DEBUG:
        urlpatterns += patterns('',
            url(r'^djangojs/', include('djangojs.urls')),
        )

Set `settings.ASSETS_DEBUG` to DEBUG in `settings.py`, the `ASSETS_DEBUG`
setting will not be reset during test runs, so the URLs patterns will still be
included (the usual `DEBUG` flag is reset).


### phantomjs

[phantomjs](http://phantomjs.org/) is a headless browser based on WebKit.
It renders HTML pages and executes JavaScript in memory, much the same as
the Chrome/Chromium browser, and allows you to automate running JavaScript
tests in a real browser environment.

Using phantomjs >= 1.9.7 is recommended because:

* phantomjs <= 1.4 requires an X server to be running, or you get a
  `cannot connect to X server` error (see [the FAQ](http://phantomjs.org/faq.html)).
  This is rather inconvenient for automated headless testing, and a waste
  of server resources.
* Older versions of phantomjs
  [fail to load modules using require.js](https://github.com/sheebz/phantom-proxy/issues/8),
  so the test runner doesn't work.

We built our own 
[phantomjs 1.9.7 RPM for CentOS 6 i386](http://rpm.aptivate.org/yum/centos/6/aptivate/i386/rpms/phantomjs-1.9.7-1.el6.i386.rpm)
which we installed on our Jenkins server. We used jbrauer's scripts to do this:

    git clone https://github.com/aptivate/phantomjs-rpms

On lin-vnc2 (Ubuntu 12.04 Precise) we installed from source as there was no
sufficiently recent Ubuntu package available.


## Testing strategies

1. Static page testing

Doesn't require any Django - just running phantomjs on a static HTML file.

2. Rendered template testing

Run on a template that djangojs will render and convert to a static HTML file.

3. Dynamic data testing

Run on a stand alone view that will use LiveServerTestCase to serve the view which
phantomjs will test against.
 
4. Real life testing

Customise your own view to include extra djangojs information (integration testing).


## Real life testing example

### Python view `<app>/tests.py`

    class CountryComparisonJsTestView(CountryComparison, QUnitView):
        template_name = 'country_comparison/test.html'
        django_js = True
        js_files = ['js/country_comparison_tests.js']

JavaScript test cases are defined in 'js_files' list.

        def get_context_data(self, **kwargs):
            kwargs.update(QUnitView.get_context_data(self))
            return super(CountryComparisonJsTestView, self).get_context_data(**kwargs)

Combine the QUnit context with our standard view.


### Urls `<app>/urls.py`

    if settings.ASSETS_DEBUG: # not set to False while tests are running
        from .tests import CountryComparisonJsTestView
        urlpatterns += patterns('',
            url(r'^country_comparison_js_test/(?P<slug>\S+)/$',
                CountryComparisonJsTestView.as_view(),
                name="country_comparison_js_test"),
        )

Add a custom URL pattern for manually accessing the test page in a browser
(under runserver). This is useful for debugging the tests.


### Template `<apps>/templates/country_comparison/test.html`

    {% extends "country_comparison/base.html" %}

Extend the template normally used by your view to include the extra information
needed to run the tests.

    {% block standard-css %}
        {{ block.super }}
        <link rel="stylesheet" href="http://code.jquery.com/qunit/qunit-1.14.0.css" type="text/css" media="screen">  
    {% endblock %}

Include the QUnit styles so it looks pretty.

    {% load js %}

    {% block js_footer %}
        {{ block.super }}

        {% block js_init %}
          {% django_js jquery=false init=django_js_init %}
        {% endblock %}

Loads djangojs helper scripts:
    - itegrates with Django's CSRF protection.
    - resolving URLs and static files.

        <div id="qunit-fixture">{% block qunit-fixture %}{% endblock %}</div>
        {% include "djangojs/qunit-runner-body.html" %}

Includes QUnit testing framework libs.


        {% for file in js_test_files %}
        {% js file %}
        {% endfor %}

Loads the JavaScript libraries under test.

        {% block js_content %}{% endblock %}
    {% endblock js_footer %}


### JavaScript tests `<app>/static/js/country_comparison_tests.js`

    test('hide add country selector', function() { 
        var forms = $('._country_add_form');
        ok(forms.length == 1, "Expect only one form");
        equal(forms.css('display'), 'none',
            "Expect the add form to be hidden initially");
    }); 

An example JavaScript test using the QUnit framework, showing the basic QUnit
assertions `ok` and `equal`. [See QUnit documentation](https://api.qunitjs.com/category/assert/)


### Django JavaScript integration `<app>/tests.py`

    @override_settings(ROOT_URLCONF="country_comparison.test_urls",
        DEBUG_PROPAGATE_EXCEPTIONS=True)
    class CountryComparisonJsTests(CountryComparisonTestMixin, QUnitSuite,
            JsTestCase):

        url_name = 'country_comparison_js_test'
        url_kwargs = {'slug': 'zambia'}

This is what points phantomjs to the correct view you want it to load and run
tests from, ie. The test view we defined earlier.

As we're testing a page generated by a DjangoCMS app, we can only access that
page by either creating a page in the database, or overriding the urls.py to
attach the app URLs to a specific prefix. We do the latter with
@override_settings as it is more efficient.


## Running JavaScript tests

### Using Django integration

    ./manage.py test ...

The standard test runner will start and run phantomjs to execute JavaScript
tests.


### Run phantomjs manually

     phantomjs .ve/local/lib/python2.7/site-packages/djangojs/phantomjs/qunit-runner.js http://localhost:8000/en/countries/country_comparison_js_test/zambia/

Expected test run output:

    ok 1 - Expect only one form
    ok 2 - Expect the add form to be hidden initially     
    ...

### How to get run tests against a static copy of a page

    wget http://localhost:8000/en/countries/country_comparison_js_test/angola/ -k -O /tmp/angola.html

wget -k rewrites relative URLS so phantomjs will be able to load referenced the
resources (runserver potentially still required) to be running).

    phantomjs .ve/local/lib/python2.7/site-packages/djangojs/phantomjs/qunit-runner.js /tmp/angola.html


### Run tests in browser and see results in page

    http://localhost:8000/en/countries/country_comparison_js_test/zambia/

Visit the test URL in browser and look for QUnit data in the page (`<div id="qunit">`)

![Screenshot](https://lh6.googleusercontent.com/U_uLLhOrVOLfkRUPYIBXqiyfbWe_WA_fXloL-kdUzAbLUmDeCCKNwz37zq5k8GzEv9JZrWIki04)

You can see from the screenshot how the QUnit test results appear at the bottom
of what is otherwise the normal page rendered by your view.
