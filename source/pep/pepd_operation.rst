.. _argus_pepd_operation:

Argus PEP Server: Operation
===========================


For more Argus details, see the :ref:`Argus Service Reference Card <service-reference-card>`.

Service Operation Commands
--------------------------

Start the PEP service
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pepd start``
   EL7        ``systemctl start argus-pepd``
   ========   ===========

Stop the PEP service
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pepd stop``
   EL7        ``systemctl stop argus-pepd``
   ========   ===========

Restart the PEP service
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pepd restart``
   EL7        ``systemctl restart argus-pepd``
   ========   ===========

PEP service status information
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pepd status``
   EL7        ``systemctl status argus-pepd``
   ========   ===========

Clear PEP service response cache
   Causes the currently cached responses from the PDP can be flushed from memory

   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``pepdctl clearResponseCache``
   EL7        ``pepdctl clearResponseCache``
   ========   ===========


Service Information
-------------------

Service Ports
~~~~~~~~~~~~~

-  Default Service Port: ``8154``
-  Default Admin Port: ``8155``

The PEP service only requires the standard service port to be open to
those PEP clients which will communicate with the PEP. The PEP must also
be able to make outbound connections to those PDPs to which is will make
policy decision requests.

Service Endpoints
~~~~~~~~~~~~~~~~~

This service contains the following endpoint URLs:

``https://argus.example.org:8154/authz``
   This endpoint is the recipient of authorization requests and is reachable on the standard
   service host and port.

``http://127.0.0.1:8155/status``
   This endpoint provides current status information on the PEP daemon and is reachable on the standard
   admin host and port.

``http://127.0.0.1:8155/clearResponseCache``
   This endpoint instructs the PEP daemon to flush its PDP response cache. It is
   reachable on the standard admin host and port.

``http://127.0.0.1:8155/shutdown``
   This endpoint instructs the PEP
   daemon to shutdown and is reachable on the standard admin host and
   port.

.. note::
   Admin services may be password protected and thus not
   invokable without this password.

Logging and Logs
----------------

This service uses the `logback <http://logback.qos.ch/>`__ logging
library. Java developers are probably familiar with Apache Log4J,
logback is written by the developer who initially wrote Log4J and
contains a cleaner API and is much more performant. The configuration
file for the logging system can be found in
``$PEP_HOME/conf/logging.xml`` and changes to this file are picked up
every 5 minutes.

Enable Debug Logging
~~~~~~~~~~~~~~~~~~~~

To enable debug logging follow:

1. Locate the line that contains ``logger name="org.glite.authz"`` (line 10 in the default logging config)
2. On the following line, change ``INFO`` to ``DEBUG``

In some cases it may be helpful to see the incoming and outgoing
messages. To do this:

1. Locate the line that contains ``org.glite.authz.message.protocol`` (line 15 in the default logging config)
2. On the following line, change ``INFO`` to ``DEBUG``

.. note::
   Always change your logging levels back to their original values
   once you are done debugging a problem. Keeping the system on the debug
   logging level could fill up your disk partition in a short time.

Service Logs
~~~~~~~~~~~~

The service writes three different logs, located in ``$PEPD_HOME/logs``:

-  **process.log** - This log file contains the normal, human-oriented
   logging messages that the system generates while in operation. This
   can be thought of as the debug log (though that's really only true if
   configured to log debug messages).
-  **access.log** - This file is an Apache-style access log showing
   information about incoming requests. This log is meant to be machine
   parsed. Each line contains the following '\|' (pipe) delimited
   fields:

   -  request time - time of the request, in the UTC timezone, from the
      Unix epoch (Jan 1, 1970, 00:00:00 UTC)
   -  remote host - the hostname or IP address of the host that
      contacted the PDP
   -  server host - the hostname or IP address on which the server
      received the request
   -  server port - the port on which the server received the request
   -  request path - the URL path that was requested

-  **audit.log** - This file contains information useful for auditing
   the system (e.g. results of authorization requests). This log is
   meant to be machine parsed. Each line contains the following '\|'
   (pipe) delimited fields:

   -  request time - time of the request, in the UTC timezone, from the
      Unix epoch (Jan 1, 1970, 00:00:00 UTC)
   -  request ID - the ID of the authorization request
   -  responder ID - the URL of the PDP that responded to the
      authorization request
   -  response ID - the ID of the response message sent back from the
      PDP
   -  policy decision - the authorization decision that was reached
