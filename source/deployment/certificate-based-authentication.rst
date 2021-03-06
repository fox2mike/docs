Certificate-Based Authentication (Experimental)
================================================

*Available as an experimental feature in Enterprise Edition E20.*

Certificate-based authentication (CBA) can be used to identify a user or a device before granting access to Mattermost, providing an additional layer of security to access the system.

Follow these steps to configure user CBA for your browser, Mattermost Desktop Apps, and the Mattermost iOS App. Before you begin, follow the `official guides to install Mattermost <https://docs.mattermost.com/guides/administrator.html#installing-mattermost>`_ on your system, including NGINX configuration as a proxy with SSL and HTTP/2, and a valid SSL certificate such as Let's Encrypt.

.. contents::
  :backlinks: top
  :local:
  :depth: 2

Set up mutual TLS authentication for the Web App
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the first step for setting up certificate-based authentication. If you haven't set up mutual TLS authentication yet, `see our documentation to learn more <https://docs.mattermost.com/deployment/ssl-client-certificate.html>`_.

Set up Mattermost server to log in with a client certificate
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Make sure your Mattermost server is licensed with a valid Enterprise Edition E20 license.
2. In ``ExperimentalSettings`` of the ``config.json`` file, set ``ClientSideCertEnable`` to ``true`` and ``ClientSideCertCheck`` to one of the following values:

- ``primary`` - After the client side certificate is verified, user's email is retrieved from the certificate and is used to log in without a password.
- ``secondary`` - After the client side certificate is verified, user's email is retrieved from the certificate and matched against the one supplied by the user. If they match, the user logs in with regular email/password credentials.

The ``config.json`` file should then have the following lines

.. code-block::

  "ExperimentalSettings": {
      "ClientSideCertEnable": true,
      "ClientSideCertCheck": "secondary"
  },

3. Restart the Mattermost server.

On Ubuntu 14.04 and RHEL 6.6:

.. code-block::

  sudo restart mattermost

On Ubuntu 16.04, Debian Jessie, and RHEL 7.1:

.. code-block::

  sudo systemctl restart mattermost

4. Go to https://example.mattermost.com and try to log in. The server should require the x.509 cert to have an ``emailAddress`` equal to the Mattermost user's email.

Run the modified Mattermost React Native Mobile App
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Fork the `master <https://github.com/mattermost/mattermost-mobile>`_ branch from the mattermost-mobile repository.
2. Set **ExperimentalClientSideCertEnable** to ``true`` in the `mattermost-mobile/assets/base/config.json <https://github.com/mattermost/mattermost-mobile/blob/master/assets/base/config.json#L15>`_ file.
3. `Use this guide <https://docs.mattermost.com/mobile/mobile-compile-yourself.html>`_ to build the apps based on the branch you created and modified in steps 1 and 2.
4. Import the certificate from the previous section above into the Mattermost iOS App and use it for mutual TLS authentication. You can `watch a demonstration video of this step here <https://drive.google.com/file/d/1zzk9XQ6RBvsWbCTrIfgE0484pD7w9Ux1/view>`_.
