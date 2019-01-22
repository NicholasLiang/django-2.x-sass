## Django sass processor tutorial (Django 2.x)

This repo is using Python 3.7.2 with Django 2.1.5. No license and feel free to make it as your starter.

### Install the requirement

By requirement.txt:

`python -m pip install -r requirements.txt`

By pip

`python -m pip install django==2.1.5 libsass==0.17.0 django_compressor==2.2 django-sass-processor==0.7.2`

### Run the Web App

`python manage.py runserver`

and open your favourite broswer to go to

`localhost:8000`

### Explanation

#### setting.py

Put the 'sass_processor' into INSTALLED_APPS

    INSTALLED_APPS = [
        ....
        'sass_processor', # add the processor here
    ]

Set the path of sass/scss files in STATICFILES_DIRS

    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, "static"),
        os.path.join(BASE_DIR, 'sass_folder'),
        ...
    ]

Set the path to store the generated css and css.map


    SASS_PROCESSOR_ROOT = os.path.join(BASE_DIR, 'static')

#### views.py

standard views files to render the page

    from django.shortcuts import render

    def index(request):
        """ index page """
        return render(request, 'index.html', {})

#### urls.py

add the views to the page

    from django.urls import path

    from . import views

    urlpatterns = [
        path('', views.index),
    ]

#### templates/index.html

Load the sass_tags by

    {% load sass_tags %}

Import the sass/scss file with html tag

    <link rel="stylesheet" href="{% sass_src 'main.scss' %}" />



The Django block `{% sass_src "<name>.scss" %}` is trying to get the `<name>.scss` file from `STATICFILES_DIRS` directories and generate a `<name>.css` and `<name>.css.map` into the `SASS_PROCESSOR_ROOT`

Reference: https://github.com/jrief/django-sass-processor/blob/master/sass_processor/processor.py

    # django-sass-processor/sass_processor/processor.py
    def __init__(self, path=None):
      self._path = path
      nmd = [d[1] for d in getattr(settings, 'STATICFILES_DIRS', [])
      if isinstance(d, (list, tuple)) and d[0] == 'node_modules']
      self.node_modules_dir = nmd[0] if len(nmd) else None

Reference: https://github.com/jrief/django-sass-processor/blob/master/sass_processor/finders.py

    # django-sass-processor/sass_processor/finders.py
    class CssFinder(FileSystemFinder):
      """
      Find static *.css files compiled on the fly using templatetag `{% sass_src "" %}`
      and stored below `SASS_PROCESSOR_ROOT`.
      """

#### sass_folder/main.scss

as example:


    body {
      background-color: black;
    }
    h1{
        border: 1px red;
        color: white;
    }
    .container {
      > ul {
        list-style: none;
        li {
          color: orange;
          &:last-child{
            color: blue;
          }
          &:after{
            clear: both;
          }
        }
      }
    }
