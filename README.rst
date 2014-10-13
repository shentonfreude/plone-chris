===============================
 README: Installing this Plone
===============================

Prerequisite Libraries
======================

Install into your system (pkg, apt-get, whatever):

* libxsl
* libxml2
* libjpeg (version?)


Virtualenv
==========

Create and activate a virtual environment. I'm using Python-2.7,
previous build was 2.6):: it's reported to be the best supported for

  virtualenv-2.7 .venv
  source .venv/bin/activate

Create Non-tracked Passwords file
=================================

Create a file `passwords.cfg` that is *not* tracked in the repo, and
place your plone's admin username and password in it like::

  [passwords]
  instance_user = admin:MySecretPassword


Build
=====

Bootstrap the buildout::

  .venv/bin/python bootstrap.py

Build it verbosely::

  bin/buildout -v

This can take 30 minutes or so if it has to download a lot of packages.

The default buildout.cfg used above builds a development version; to build a production version that runs as user `plone`, do::

  bin/buildout -v -c production.cfg

It should ask for your sudo password at the end to fix permissions
such that a 'plone' user owns various files in var/.

Test
====

To test the development version::

  sudo bin/zeoserver start
  sudo bin/instance1 fg

To test the production build, you need sudo so they can set their user::

  sudo bin/zeoserver start
  sudo bin/instance1 fg

You should see it start up, connect to zeostorage, and eventually say::

  2014-10-13 09:11:21 INFO Zope Ready to handle requests

Login as admin to the site on localhost with your instance's port from
buildout.cfg and see if it tells you that you need to upgrade. Then
Site Setup and then the Zope Management Interface for items needing
upgrades.

Quit out of the instance1, and stop zeoserver::

  sudo bin/zeoserver stop


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

Upgrading
=========

If you want to upgrade Plone, change the version in the buildout.cfg
file and redo the buildout as above.  Restart zeo and run instance1 in
foreground again. Login to the site and go to Site Setup then Zope
Management Interface.  It should tell you the site needs upgrading,
the current config (e.g, 4016) and the latest (e.g., 4206) to which
you're about to upgrade. Do a "Dry run mode" to make sure the upgrade
will work (check the box at the bottom), and if that's fine, do it for
real.

Add Ons
=======

You can check what add-ons are available and update any old ones by
going to Site Setup then Add-ons.  After upgrading from 4.0 to 4.2, I
see that Diazo, JQuery Tools, Working Copy (Iterate), and a bunch of
others are now available.

If you need or want them, click their boxes and Activate.

Future
======

I really want to become enlightened about Diazo, which I think of as a
"theming proxy" that can composite content from one or more backend
servers into a unified skin completely separate from those servers.

Dexterity promises to allow users to create new content types through
the web, which our users would love to have, similar it sounds to the
popular CCK feature in Drupal.
