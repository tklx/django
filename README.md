# tklx/django - Python web framework
[![CircleCI](https://circleci.com/gh/tklx/django.svg?style=shield)](https://circleci.com/gh/tklx/django)

[Django][django] is a free and open-source web framework, written in Python, which follows the model-view-controller (MVC) architectural pattern. Django's primary goal is to ease the creation of complex, database-driven websites. Django emphasizes reusability and "pluggability" of components, rapid development, and the principle of don't repeat yourself. 

## Features

- Based on the super slim [tklx/base][base] (Debian GNU/Linux).
- Django installed from Debian.
- Uses [tini][tini] for zombie reaping and signal forwarding.
- Includes ``USER django`` to restrict the privileges.
- Includes ``EXPOSE 8000``, so standard container linking will make it
  automatically available to the linked containers.

## Usage

This image uses [uwsgi][uwsgi] to run Django apps.

### Running a simple Django site (uWSGI protocol)

```console
$ docker run --name some-django -v /path/to/django/project:/home/django/appname -d tklx/django --chdir /home/django/appname --wsgi-file appname/wsgi.py --uwsgi-socket :8000
```

### Running a simple Django site (HTTP protocol)

```console
$ docker run --name some-django -v /path/to/django/project:/home/django/appname -d tklx/django --chdir /home/django/appname --wsgi-file appname/wsgi.py --http-socket :8000
```

### Running a site with custom uwsgi config file
```console
$ cat /path/to/uwsgi/conf.ini
[uwsgi]
socket = :8000
chdir = /home/django/app
wsgi-file = appname/wsgi.py
$ docker run --name some-django -v /path/to/django/project:/home/django/appname -v /path/to/uwsgi/conf.ini:/etc/uwsgi/apps-available/default.ini -d tklx/django
```

### A complete reverse proxy setup
```console
$ docker run --name some-django -v /path/to/django/project:/home/django/appname -d tklx/django --chdir /home/django/appname --wsgi-file appname/wsgi.py
$ docker run -p 80:80 --name some-nginx --link some-django:django -v /path/to/nginx/config:/etc/nginx/sites-available/default -d tklx/nginx
$ cat /path/to/nginx/config
server {
    listen 80;
    server_name www.example.com;

    location /media {
        # your Django project's media files - amend as required
        alias /path/to/your/mysite/media;
    }

    location /static {
        # your Django project's static files - amend as required
        alias /path/to/your/mysite/static;
    }

    # send all non-media requests to uwsgi
    location / {
        uwsgi_pass django:8000;
        include uwsgi_params;
    }
}
```

### Using the built-in Django webserver (not recommended)

```console
$ docker run -v /path/to/django/project:/home/django/appname -d tklx/django python appname/manage.py migrate && python appname/manage.py runserver 0.0.0.0:8000
```

## Automated builds

The [Docker image](https://hub.docker.com/r/tklx/django/) is built, tested and pushed by [CircleCI](https://circleci.com/gh/tklx/django) from source hosted on [GitHub](https://github.com/tklx/django).

* Tag: ``x.y.z`` refers to a [release](https://github.com/tklx/django/releases) (recommended).
* Tag: ``latest`` refers to the master branch.

## Status

Currently on major version zero (0.y.z). Per [Semantic Versioning][semver],
major version zero is for initial development, and should not be considered
stable. Anything may change at any time.

## Issue Tracker

TKLX uses a central [issue tracker][tracker] on GitHub for reporting and
tracking of bugs, issues and feature requests.

[django]: https://www.djangoproject.com/
[base]: https://github.com/tklx/base
[tini]: https://github.com/krallin/tini
[uwsgi]: http://uwsgi-docs.readthedocs.io/en/latest/
[semver]: http://semver.org/
[tracker]: https://github.com/tklx/tracker/issues

