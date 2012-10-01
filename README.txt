===============================
 README: Installing this Plone
===============================

Virtualenv
==========

Create and activate a virtual environment. I'm using Python-2.6 as
it's reported to be the best supported for this version of Plone::

  /usr/local/python/2.6.5/bin/virtualenv --no-site-packages --distibute .
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

  bin/python bootstrap.py

Build it verbosely::

  bin/buildout -v

It should ask for your sudo password at the end to fix permissions
such that a 'plone' user owns various files in var/.

Test
====

Test them out, sudo needed so they can set their user::

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
