Django TarView 
==============

[![Build Status](https://travis-ci.org/luckydonald/django-tarview.svg)](https://travis-ci.org/luckydonald/django-tarview)[![On PyPI](https://img.shields.io/pypi/v/django-tarview.svg)](https://pypi.python.org/pypi/django-tarview)    
A simple Django base view to stream file-like-objects as .tar archive.    
Python 2.7 and 3 ready.

Installation
------------

    pip install django-tarview

Usage and examples
------------------

To create a tar download view:

 * Extend `BaseTarView`
 * implement `get_files`
 * That's it

The `get_files` method must return a list (or iterator) of file-like objects.

Example with a ContentFile:


```python
from tarview.views import BaseTarView

from django.core.files.base import ContentFile
from django.utils.six import b  # or use b"foo" instead.

class JustTextFilesView(BaseTarView):
    """
    Generate a tar with 10 files 'fileX.txt' with a simple text,
    using Djangos ContentFile
    """
    def get_files(self):
        for i in range(0,10):
            file_like = ContentFile(b("This is file %d." % i), name="file%d.txt" % i)
            yield file_like
```

Or pull them from a database like this:
    
```python
from tarview.views import BaseTarView

from reviews import Review

class CommentsArchiveView(BaseTarView):
    """Download at once all comments for a review."""

    def get_files(self):
        document_key = self.kwargs.get('document_key')
        reviews = Review.objects \
            .filter(document__document_key=document_key) \
            .exclude(comments__isnull=True)

        return [review.comments.file for review in reviews if review.comments.name]
```

View configuration
------------------

By default, the downloaded file is named `download.tar` you can set a custom name
by setting the `tarfile_name` parameter.

```python

class TarView(BaseTarView):
    tarfile_name = 'something.tar'
```

Testing
-------

Django TarView uses [tox, the testing automation tool](https://tox.readthedocs.org/en/latest/),
to run tests.

To launch tests:

    pip install tox
    tox


Author
------

Crafted with love by luckydonald.
Modification of [Thibault Jouannic](http://www.miximum.fr)'s work.
