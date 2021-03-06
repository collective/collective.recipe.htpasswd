========================================
Buldout recipe for create htpasswd files
========================================

.. contents::


Introdution
===========

This recipe can be used to generate files for basic authentication of HTTP
users, to restrict the access to HTTP resoruces. The aim is to be fully
compatible with the `htpasswd program`_ that come with the
`Apache httpd Server`_, and support all the `password formats`_ that it
supports. This formats, with some minor diffenrences in the case of the plain
method, are also `supported by the auth_basic module`_ of the nginx http server.

This recipe support crypt, md5 (`APR md5 algorithm`_), plain and sha1 algorithms
for storage passwords. The crypt algorithm is based on the system's crypt()
routine, so it inherits its limitations (see: `man 5 crypt`_).


*Note:* The plaintext passowrds are only accepted by the Apache httpd server on
Windows and Netware.

*Caution:* This recipe should not be used to update an existing htpasswd file,
because it overwritte the htpasswd file in every update.

Example usage
=============

The simplest way to use this recipe is to add a part in ``buildout.cfg`` like
this:

.. code-block:: ini

    [buildout]
    parts = htpasswd

    [htpasswd]
    recipe = collective.recipe.htpasswd
    output = ${buildout:directory}/etc/htpasswd
    credentials =
        nueces:secret
        nutz:crackme

One example using the ``sha1`` algorithm:

.. code-block:: ini

    [buildout]
    parts = htpasswd

    [htpasswd]
    recipe = collective.recipe.htpasswd
    output = ${buildout:directory}/etc/htpasswd
    algorithm = sha1
    credentials =
        nueces:secret
        nutz:crackme


For use the ``md5`` algorithm this recipe relies in the
`python-aprmd5 package`_, then to install it you must modify the buildout part
to include the md5 ``extras_require`` setting and install the build depencies
for the python-aprmd5 package. In Debian GNU/Linux the package is the
`libaprutil1-dev`_. It contain the develoment headers of the
`Apache Portable Runtime Utility Library`_.

After that modify the part in the ``buildout.cfg`` it must look like this:

.. code-block:: ini

    [buildout]
    parts = htpasswd

    [htpasswd]
    recipe = collective.recipe.htpasswd [md5]
    output = ${buildout:directory}/etc/htpasswd
    algorithm = md5
    credentials =
        nueces:secret
        nutz:crackme


*Note:* For a `bug in zc.buildout`_ if you need to use this recipe with the md5
and the plain or crypt algorithms in two o more parts, you must declare first
the one that use the md5 ``extras_require``.


Supported options
=================

* output: Specify a path to the output file. The path will be created if it does
  not exist.
* credentials: One set per line of credentials formed by username and password
  separated by a colon. e.g. ``<username>:<password>``.
* mode: Specified with octal numbers, as in the chmod program. e.g. ``640``.
  If it not set the file are created with the mask mode from the system
  enviroment.
* algorithm: The supported options are ``crypt``, ``md5``, ``plain`` and
  ``sha1``. Default to ``cypt``.


Development
===========

- Code repository: http://github.com/collective/collective.recipe.htpasswd
- Report bugs at http://github.com/collective/collective.recipe.htpasswd/issues


.. _htpasswd program: http://httpd.apache.org/docs/2.4/programs/htpasswd.html
.. _Apache httpd server: http://httpd.apache.org/
.. _APR md5 algorithm: http://apr.apache.org/docs/apr-util/trunk/group___a_p_r___m_d5.html
.. _password formats: http://httpd.apache.org/docs/2.2/misc/password_encryptions.html
.. _supported by the auth_basic module: http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html#auth_basic
.. _man 5 crypt: http://manpages.debian.net/cgi-bin/man.cgi?query=crypt&sektion=3
.. _python-aprmd5 package: http://www.herzbube.ch/python-aprmd5
.. _libaprutil1-dev: http://packages.debian.org/stable/libaprutil1-dev
.. _Apache Portable Runtime Utility Library: http://apr.apache.org/
.. _bug in zc.buildout: https://bugs.launchpad.net/zc.buildout/+bug/583752
