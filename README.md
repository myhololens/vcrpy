#VCR.py

This is a proof-of-concept start at a Python version of [Ruby's VCR
library](https://github.com/myronmarston/vcr).

[![Build Status](https://secure.travis-ci.org/kevin1024/vcrpy.png?branch=master)](http://travis-ci.org/kevin1024/vcrpy)

##What it is supposed to do
Simplify testing by recording all HTTP interactions and saving them to
"cassette" files, which are just yaml files.  Then when you run your tests
again, they all just hit the text files instead of the internet.  This speeds up
your tests and lets you work offline.

##Compatibility Notes
This should work with Python 2.6 and 2.7.  There aren't any context managers in
2.5 so I'm not bothering with that.

Currently I've only tested this (a little bit) with urllib2.  It's known to *NOT
WORK* with urllib.

##How to use it
```python
import vcr
import urllib2

with vcr.use_cassette('fixtures/vcr_cassettes/synopsis.yaml'):
    response = urllib2.urlopen('http://www.iana.org/domains/example/').read()
    assert 'Example Domains' in response
```

Run this test once, and VCR.py will record the http request to
`fixtures/vcr_cassettes/synopsis.yml`. Run it again, and VCR.py will replay the
response from iana.org when the http request is made. This test is now fast (no
real HTTP requests are made anymore), deterministic (the test will continue to
pass, even if you are offline, or iana.org goes down for maintenance) and
accurate (the response will contain the same headers and body you get from a
real request).

##Ruby VCR compatibility
I'm not trying to match the format of the YAML files.  Cassettes generated by
Ruby's VCR are not compatible with VCR.py.  The API is similar but VCR.py
doesn't have nearly as many features.

##Known Issues
 * Probably only works with urllib2.
 * [requests](http://docs.python-requests.org/en/latest/index.html) kinda works as long as you just do HTTP GETS and don't get too fancy.
 * Loading a HTTPS url and then a HTTP url in the same cassette will cause the second load to use the data from the HTTPS version of the URL ( Issue # 3)
 * Loading urls that do a 301/302 redirect don't work ( Issue #4 )
 * I haven't tested PUT/DELETE requests

##Similar libraries in Python
Neither of these really implement the API I want, but I have cribbed some code
from them.
 * https://github.com/bbangert/Dalton
 * https://github.com/storborg/replaylib

#License
This library uses the MIT license
