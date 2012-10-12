===============================
 README: Installing this Plone
===============================

Virtualenv
==========

Create and activate a virtual environment. I'm using Python-2.7 as
it's now officially supported by Plone-4.2::

  /usr/local/python/2.7.2/bin/virtualenv --no-site-packages --distibute .
  source bin/activate

Create Non-tracked Passwords file
=================================

Create a file `passwords.cfg` that is *not* tracked in the repo, and
place your plone's admin username and password in it like::

  [passwords]
  instance_user = admin:MySecretPassword


Build
=====

Bootstrap the buildout::

  bin/python bootstrap.py --version=1.6.3

I want bootstrap 1.6.x for speed improvements, but this bootstrap
keeps installing 1.4.4, and I don't know why.  Install it manually::

  bin/easy_install zc.buildout==1.6.3

But then I get conflicts with buildout-1.4.4 required by mr.developer. Stuck.

[I think the --version may actually work, check venv site-packages ... but does binary do 1.4.4??]


Don't use verbose but use -N to avoid checking for already-satisfied versions; ::

  bin/buildout -N -t 5

My buildout.cfg is meant for the common case of development. If you
want to build for production use::

  bin/buildout -N -t 5 -c production.cfg

For production, it should ask for your sudo password at the end to fix
permissions such that a 'plone' user owns various files in var/.

Test
====

Test them out; for sauna.reload we need to set the environment to tell
it where to find sources::

  RELOAD_PATH=src/ bin/instance1 fg


To try out the production version, you'll need sudo so they can set
the user, but you won't want sauna reload::


  sudo bin/zeoserver start
  sudo bin/instance1 fg

Login as admin to the site on localhost with your instance's port from
buildout.cfg and see if it tells you that you need to upgrade. Then
Site Setup and then the Zope Management Interface for items needing
upgrades.

Run for Production
==================

This buildout uses `supervisor` to run the daemons and will restart them if memory grows too large.  The supervisord should be started at boot time with an init.d/ type of script, something like::

	${instancedir}/bin/supervisord

You can check on it with::

	${instancedir}/bin/supervisorctl status

And shut down everything with::

	${instancedir}/bin/supervisorctl shutdown


Packing the Database
====================

If you're remote, you may have to access the top-level admin area by
tunneling to the port since an Apache rewrite will likely prevent you
reaching this high up; replace 60001 with your instance's port::

  ssh -L 60001:localhost:60001 serverhostname

then connect to http://localhost:60001

From there go to the Zope Management Interface, Contrl Panel, Database Management, main to pack the database.

TO DO
=====

