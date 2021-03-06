Django Compress Storage
=======================

[![Build Status](https://travis-ci.org/valdergallo/django-compress-storage.png?branch=master)](https://travis-ci.org/valdergallo/django-compress-storage)
[![Latest Version](http://img.shields.io/pypi/v/django-compress-storage.svg)](https://pypi.python.org/pypi/django-compress-storage)
[![BSD License](http://img.shields.io/badge/license-BSD-yellow.svg)](http://opensource.org/licenses/BSD-3-Clause)

Custom Field for Django that auto compact file uploaded


Features
--------

- Compress FileUpload storage file with Zip
- Delete old file that was compressed on zip
- Support for South Migrations
- Support Django 1.2+
- Celery 2.8+ support - async compress file with Celery
- Windows Support
- Linux support
- OSx support
- Support for Python3
- Support for Python2.6+


Motivation
----------

On my job we need save all upload files for 5 year. Losing a lot space on server with this files, because this I created this application.


Django Settings Configurations
------------------------------

```python

FILE_COMPRESS_DELETE_OLD_FILE = True # to delete old files after compressed
FILE_COMPRESS_DELETE_OLD_FILE = False # to not delete old files after compressed

# Feature only for version v9.0+
FILE_COMPRESS_QUEUE = 'Celery' # by default queue is Celery, but you can change this with this var on settings


INSTALLED_APPS = (
    ...
    ...
    'compress_storage',
)
```


Usage
-----

```python

# example model.py

from django.db import models
from compress_storage import ZipFileField

class MyContent(models.Model):
    name = models.CharField(max_length=150)
    create_date = models.DateTimeField(auto_now=True)
    upload_file = ZipFileField(upload_to='mycontent/')

    def __unicode__(self):
        return self.name

```


Shell
-----

```python

>>> from example.core import MyContent
>>> m = MyContent.objects.get(id=2)
>>> m.upload_file
<ZipCompressFieldFile: mycontent/test.txt>
>>> m.upload_file.compress()
>>> m.upload_file
<ZipCompressFieldFile: mycontent/test.zip>
```


Using with Celery
-----------------

If Celery are installed on Site Packages. You just need create one post_save on
your model to use async compress.


```python
# listeners.py file

from django.db.models.signals import post_save

def auto_compress_file_on_post_save(sender, instance, **kargs):
    instance.upload_file.compress()

post_save.connect(auto_compress_file_on_post_save, sender=MyContent)

```

If you don´t wanna use Celery async compress:


```python

def auto_compress_file_on_post_save(sender, instance, **kargs):
    instance.upload_file.compress(async=False)

post_save.connect(auto_compress_file_on_post_save, sender=MyContent)

```


Developer
---------

```bash
# download code
git clone https://github.com/valdergallo/django-compress-storage

# install developer packages
setup.py develop

# test project
setup.py test

#clean extra content
setup.py clean

```


