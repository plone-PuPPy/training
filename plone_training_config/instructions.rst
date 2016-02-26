.. _instructions-label:

Installing Plone for the Training
=================================

Keep in mind that you need a fast internet connection during installation since you'll have to download a lot of data!


Installing Plone with vagrant
-----------------------------

In order not to waste too much time with installing and debugging the differences between systems, we use a virtual machine (Ubuntu 14.04) to run Plone during the training. We rely on Vagrant and VirtualBox to give the same development environment to everyone.

`Vagrant <https://www.vagrantup.com>`_ is a tool for building complete development environments. We use it together with Oracle’s `VirtualBox <https://www.virtualbox.org>`_ to create and manage a virtual environment.

.. _install-virtualbox:

Install VirtualBox
++++++++++++++++++

Vagrant uses Oracle’s VirtualBox to create virtual environments. Here is a link directly to the download page: https://www.virtualbox.org/wiki/Downloads. We use VirtualBox 4.3.x


.. _instructions-configure-vagrant-label:

Install and configure Vagrant
+++++++++++++++++++++++++++++

Get the latest version from http://www.vagrantup.com/downloads for your operating system and install it.

.. note::

    In Windows there is a bug in the recent version of Vagrant. Here are the instructions for how to work around the warning ``Vagrant could not detect VirtualBox! Make sure VirtualBox is properly installed``.

Now your system has a command ``vagrant`` that you can run in the terminal.

First, create a directory in which you want to do the training.

.. code-block:: bash

    $ mkdir training
    $ cd training

Setup Vagrant to automatically install the current guest additions. You can choose to skip this step if you encounter any problems with it.

.. code-block:: bash

    $ vagrant plugin install vagrant-vbguest

Now download :download:`plone_training_config.zip <../_static/plone_training_config.zip>` and copy its contents into your training directory.

.. code-block:: bash

    $ wget https://raw.githubusercontent.com/plone-PuPPy/training/master/_static/plone_training_config.zip
    $ unzip plone_training_config.zip

The training directory should now hold the file ``Vagrantfile`` and the directory ``manifests`` which again contains several files.

Now start setting up the VM that is configured in ``Vagrantfile``:

.. code-block:: bash

    $ vagrant up

This takes a **veeeeery loooong time** (between 10 minutes and 1h depending on your internet connection and system speed) since it does all the following steps:

* downloads a virtual machine (Official Ubuntu Server 14.04 LTS, also called "Trusty Tahr")
* sets up the VM
* updates the VM
* installs various system-packages needed for Plone development
* downloads and unpacks the buildout-cache to get all the eggs for Plone
* clones the coredev buildout into /vagrant/buildout
* builds Plone using the eggs from the buildout-cache

.. note::

    Sometimes this stops with the message:

    .. code-block:: bash

        Skipping because of failed dependencies

    If this happens or you have the feeling that something has gone wrong and the installation has not finished correctly for some reason you need to run the following command to repeat the process. This will only repeat steps that have not finished correctly.

    .. code-block:: bash

        $ vagrant provision

    You can do this multiple times to fix problems, e.g. if your network connection was down and steps could not finish because of this.

.. note::

    If while bringing vagrant up you get an error similar to:

    .. code-block:: bash

        ssh_exchange_identification: read: Connection reset by peer

    The configuration may have stalled out because your computer's BIOS requires virtualization to be enabled. Check with your computer's manufacturer on how to properly enable virtualization.  See: https://teamtreehouse.com/community/vagrant-ssh-sshexchangeidentification-read-connection-reset-by-peer

Once Vagrant finishes the provisioning process, you can login to the now running virtual machine.

.. code-block:: bash

    $ vagrant ssh

.. note::

    If you use Windows you'll have to login with `putty <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>`_. Connect to vagrant@127.0.01 at port 2222. User **and** password are ``vagrant``.

You are now logged in as the user vagrant in ``/home/vagrant``. We'll do all steps of the training as this user.

Instead we use our own Plone instance during the training. It is in ``/vagrant/buildout/``. Start it in foreground with ``./bin/instance fg``.

.. code-block:: pypy

    vagrant@training:~$ cd /vagrant/buildout
    vagrant@training:/vagrant/buildout$ ./bin/instance fg
    2016-02-26 05:50:41 INFO ZServer HTTP server started at Fri Feb 26 05:50:41 2016
        Hostname: 0.0.0.0
        Port: 8080
    2016-02-26 05:50:48 INFO ZODB.blob (18746) Blob directory /home/vagrant/var/blobstorage does not exist. Selected `bushy` layout.
    2016-02-26 05:50:48 INFO ZODB.blob (18746) Blob directory '/home/vagrant/var/blobstorage/' does not exist. Created new directory.
    2016-02-26 05:50:48 INFO ZODB.blob (18746) Blob temporary directory '/home/vagrant/var/blobstorage/tmp' does not exist. Created new directory.
    2016-02-26 05:51:00 INFO Plone OpenID system packages not installed, OpenID support not available
    2016-02-26 05:51:06 INFO Zope Ready to handle requests

.. note::

    In rare cases when you are using OSX with an UTF-8 character set starting Plone might fail with the following error:

    .. code-block:: text

       ValueError: unknown locale: UTF-8

    In that case you have to put the localized keyboard and language settings in the .bash_profile of the vagrant user to your locale (like ``en_US.UTF-8`` or ``de_DE.UTF-8``)

    .. code-block:: bash

        export LC_ALL=en_US.UTF-8
        export LANG=en_US.UTF-8

Now the Zope instance we're using is running. You can stop the running instance anytime using ``ctrl + c``.

If it doesn't, don't worry, your shell isn't blocked. Type ``reset`` (even if you can't see the prompt) and press RETURN, and it should become visible again.

If you point your local browser at http://localhost:8080 you see that Plone is running in vagrant. This works because VirtualBox forwards the port 8080 from the guest system (the vagrant Ubuntu) to the host system (your normal operating system). Now create a new Plone site by clicking "Create a new Plone site". The username and the password are both "admin" (Never do this on a real site!).

The Buildout for this Plone is in a shared folder.  This means we run it in the vagrant box from ``/vagrant/buildout`` but we can also access it in our own operating system and use our favorite editor. You will find the directory ``buildout`` in the directory ``training`` that you created in the very beginning next to ``Vagrantfile`` and ``manifests``.

.. note::

    The database and the python packages are not accessible in your own system since large files cannot make use of symlinks in shared folders. The database lies in ``/home/vagrant/var``, the python packages are in ``/home/vagrant/packages``.

If you have any problems or questions please create a ticket at https://github.com/plone-PuPPy/training/issues.


.. _instructions-vagrant-does-label:

What Vagrant does
+++++++++++++++++

Installation is done automatically by vagrant and puppet. If you want to know which steps are actually done please see the chapter :doc:`what_vagrant_does`.

.. _instructions-vagrant-care-handling-label:

.. note::

    **Vagrant Care and Handling**

    Keep in mind the following recommendations for using your Vagrant virtualboxes:

    * Use the ``vagrant suspend`` or ``vagrant halt`` commands to put the virtualbox to "sleep" or to "power it off" before attempting to start another Plone instance anywhere else on your machine, if it uses the same port.  That's because vagrant "reserves" port 8080, and even if you stopped Plone in vagrant, that port is still in use by the guest OS.
    * If you are done with a vagrant box, and want to delete it, always remember to run ``vagrant destroy`` on it before actually deleting the directory containing it.  Otherwise you'll leave its "ghost" in the list of boxes managed by vagrant and possibly taking up disk space on your machine.
    * See ``vagrant help`` for all available commands, including ``suspend``, ``halt``, ``destroy``, ``up``, ``ssh`` and ``resume``.
