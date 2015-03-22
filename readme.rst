Django development dashboard
============================

Warning
-------
This project is deprecated. The code for dashboard.djangoproject.com is now
within the `django/djangoproject.com`_ repository.

.. _`django/djangoproject.com`: https://github.com/django/djangoproject.com

Intro
-----

http://dddash.herokuapp.com/.

A status dashboard tracking some metrics of Django's development process.

Setup
-----

To run locally::

    mkvirtualenv djdash
    pip install -r requirements.txt
    export TRAC_CREDS=<user:pass> # Your djangoproject.com creds.
    ./manage.py syncdb --migrate
    ./manage.py loaddata example_data
    ./manage.py update_metrics

To deploy onto Heroku (mostly for my future reference):

Initial setup::

    heroku apps:create --stack cedar dddash
    git push heroku master
    heroku addons:add heroku-shared-postgresql
    heroku pg:promote HEROKU_SHARED_POSTGRESQL_AQUA
    heroku addons:add pgbackups
    heroku addons:add redistogo:nano
    heroku addons:add scheduler:standard
    heroku addons:add sentry
    heroku addons:open scheduler
    # add a job to run `python manage.py update_metrics` hourly
    heroku config:add DJANGO_SETTINGS_MODULE=dashboard.settings.heroku
    heroku config:add TRAC_CREDS=<user:pass>
    heroku config:add AWS_ACCESS_KEY_ID=<access key id>
    heroku config:add AWS_SECRET_ACCESS_KEY=<secret access key>
    heroku config:add AWS_STORAGE_BUCKET_NAME=<bucket name>
    heroku run -- python manage.py syncdb --migrate

(Yeah, you do have to do the deploy first. If you try to `heroku config` before a deploy the settings don't seem to stick.)

Each deploy::

    git push heroku master
    heroku run python manage.py collectstatic # (if static media changed)
