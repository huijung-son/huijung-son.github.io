---
title: "Web Project - Chat API Server (Django)"
date: 2026-01-06 00:00:00
categories: [Project, Web]
tags: [Chat]
description: ""
---

### start

```shell
django-admin startproject conf .
django-admin startapp foo
```

### inspectdb

```shell
python manage.py inspectdb -h
```

```text
usage: manage.py inspectdb [-h] [--database {default}] [--include-partitions] [--include-views] [--version] [-v {0,1,2,3}] [--settings SETTINGS] [--pythonpath PYTHONPATH] [--traceback] [--no-color] [--force-color]
                           [table ...]

Introspects the database tables in the given database and outputs a Django model module.

positional arguments:
  table                 Selects what tables or views should be introspected.

options:
  -h, --help            show this help message and exit
  --database {default}  Nominates a database to introspect. Defaults to using the "default" database.
  --include-partitions  Also output models for partition tables.
  --include-views       Also output models for database views.
  --version             Show program's version number and exit.
  -v, --verbosity {0,1,2,3}
                        Verbosity level; 0=minimal output, 1=normal output, 2=verbose output, 3=very verbose output
  --settings SETTINGS   The Python path to a settings module, e.g. "myproject.settings.main". If this isn't provided, the DJANGO_SETTINGS_MODULE environment variable will be used.
  --pythonpath PYTHONPATH
                        A directory to add to the Python path, e.g. "/home/djangoprojects/myproject".
  --traceback           Display a full stack trace on CommandError exceptions.
  --no-color            Don't colorize the command output.
  --force-color         Force colorization of the command output.
```

```text
# This is an auto-generated Django model module.
# You'll have to do the following manually to clean this up:
#   * Rearrange models' order
#   * Make sure each model has one field with primary_key=True
#   * Make sure each ForeignKey and OneToOneField has `on_delete` set to the desired behavior
#   * Remove `managed = False` lines if you wish to allow Django to create, modify, and delete the table
# Feel free to rename the models, but don't rename db_table values or field names.
```
