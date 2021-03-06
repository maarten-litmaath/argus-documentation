.. _argus_emi_nagios_probes:

Nagios Probes for Argus (UMD)
=============================

Installation
------------

Prerequisites
~~~~~~~~~~~~~

-  Python 2.4 or newer (not Python 3000)
-  UMD-3 or later (or EMI-3) repository installed
-  Host certificate & IGTF-bundle
-  Nagios server

Where to install
~~~~~~~~~~~~~~~~

-  This set of probes needs to be installed on the Nagios server only.
   On the client side (where the Argus services actually run), no action
   is required.

Install instructions
~~~~~~~~~~~~~~~~~~~~

There is available a rpm called "nagios-plugins-argus", (`nagios-plugins-argus - v. 1.1.0 (EMI-3)
<http://eticssoft.web.cern.ch/eticssoft/repository/emi/emi.argus.nagios-plugins/1.1.0/noarch/>`__.
It can be installed as follows:

::

    yum install nagios-plugins-argus

This installs the plugins into the
``/usr/libexec/grid-monitoring/probes/`` directory. It is up to the user
to bind them into the local Nagios environment.

**NOTE:** If you use iptables you need to open the ports on client side
(where the Argus services are installed):

for IPv4 do:

::

    iptables -I INPUT 1 -p tcp --dport 8154 -j ACCEPT
    iptables -I INPUT 1 -p tcp --dport 8152 -j ACCEPT
    iptables -I INPUT 1 -p tcp --dport 8150 -j ACCEPT
    /etc/init.d/iptables save
    /etc/init.d/iptables restart

for IPv6 do:

::

    ip6tables -I INPUT 1 -p tcp --dport 8154 -j ACCEPT
    ip6tables -I INPUT 1 -p tcp --dport 8152 -j ACCEPT
    ip6tables -I INPUT 1 -p tcp --dport 8150 -j ACCEPT
    /etc/init.d/ip6tables save
    /etc/init.d/ip6tables restart

Overview of probes
------------------

There are 9 different probes available, three for each service:

+---------+-----------------------------------+
| Service | Probe-Name                        |
+=========+===================================+
| PAP     | nagios-plugins-argus.PAP.memory   |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PAP.policies |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PAP.status   |
+---------+-----------------------------------+
| PDP     | nagios-plugins-argus.PDP.memory   |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PDP.status   |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PDP.traffic  |
+---------+-----------------------------------+
| PEPD    | nagios-plugins-argus.PEP.memory   |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PEP.status   |
+---------+-----------------------------------+
| ^       | nagios-plugins-argus.PEP.traffic  |
+---------+-----------------------------------+

PAP probes
~~~~~~~~~~

nagios-plugins-argus.PAP.status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checks if the service is available. This is done by a call to
``https://`hostname`:8150/pap/status``, if there is a valid response
the service is considered up and running.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+


nagios-plugins-argus.PAP.memory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current memory consumption is read out and displayed in
the Status information field of the Nagios control panel and written
into the Performance Data tab of Nagios. Hence it can be displayed as a
graph over time by Nagios add-ons, e.g. pnp.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --warning MEM_WARN  | Memory usage warning threshold in MB                                          | ``224.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --critical MEM_CRIT | Memory usage critical threshold in MB                                         | ``256.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+


nagios-plugins-argus.PAP.policies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current number of policies in the monitored PAP is read
out and displayed in the Status information field of the Nagios control
panel and written into the Performance Data tab of Nagios. Hence it can
be displayed as a graph over time by Nagios add-ons, e.g. pnp.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+


PDP probes
~~~~~~~~~~

nagios-plugins-argus.PDP.status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checks if the service is available. This is done by a call to
``https://`hostname`:8152/status``, if there is a valid response the
service is considered up and running.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+

nagios-plugins-argus.PDP.memory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current memory consumption is read out and displayed in
the Status information field of the Nagios control panel and written
into the Performance Data tab of Nagios. Hence it can be displayed as a
graph over time by Nagios add-ons, e.g. pnp.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --warning MEM_WARN  | Memory usage warning threshold in MB                                          | ``224.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --critical MEM_CRIT | Memory usage critical threshold in MB                                         | ``256.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+


nagios-plugins-argus.PDP.traffic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current number of requests executed and the number how
many of them have been successful is read out and compared to the same
numbers of the former test executed. Out of this the requests per
second, the completed requests per second and the erroneous requests per
second are computed and written into the Performance Data tab of Nagios.
Hence it can be displayed as a graph over time by Nagios add-ons, e.g.
pnp. Additionally the number of requests since the last test is written
into the Status information field of the Nagios control panel.

Options
'''''''

+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
| Mandatory | CLI option            | Description                                                                   | Default                                                      |
+===========+=======================+===============================================================================+==============================================================+
| YES       | --hostname=HOSTNAME   | The hostname of the service                                                   |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --cert CERT           | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem``                          |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --key KEY             | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``                           |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --capath CAPATH       | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates``                          |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --tempdir=TEMP\_DIR   | Storage path for the needed temporary file                                    | ``../../../../var/lib/grid-monitoring/nagios-plugins-argus`` |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --tempfile=TEMP\_FILE | Name for the needed temporary file                                            | hostname.nagios-plugins-argus.PDP.traffic.pickle             |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --help                | show this help message and exit                                               |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --port PORT           | The port of the service                                                       | ``8152``                                                     |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --url URL             | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --version             | show program's version number and exit                                        |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --timeout TIMEOUT     | The TCP timeout for the HTTPS connection in seconds                           | ``20``                                                       |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --verbose             | verbose mode                                                                  | False                                                        |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+

PEP Server probes
~~~~~~~~~~~~~~~~~

nagios-plugins-argus.PEP.status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checks if the service is available. This is done by a call to
``https://`hostname`:8154/status``, if there is a valid response the
service is considered up and running.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --warning MEM_WARN  | Memory usage warning threshold in MB                                          | ``224.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --critical MEM_CRIT | Memory usage critical threshold in MB                                         | ``256.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+

nagios-plugins-argus.PEP.memory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current memory consumption is read out and displayed in
the Status information field of the Nagios control panel and written
into the Performance Data tab of Nagios. Hence it can be displayed as a
graph over time by Nagios add-ons, e.g. pnp.

Options
'''''''

+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
| Mandatory | CLI option          | Description                                                                   | Default                             |
+===========+=====================+===============================================================================+=====================================+
| YES       | --hostname=HOSTNAME | The hostname of the service                                                   |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --warning MEM_WARN  | Memory usage warning threshold in MB                                          | ``224.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --critical MEM_CRIT | Memory usage critical threshold in MB                                         | ``256.0``                           |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --cert CERT         | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --key KEY           | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``  |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --capath CAPATH     | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates`` |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --help              | show this help message and exit                                               |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --port PORT         | The port of the service                                                       | ``8152``                            |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --url URL           | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --version           | show program's version number and exit                                        |                                     |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --timeout TIMEOUT   | The TCP timeout for the HTTPS connection in seconds                           | ``20``                              |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+
|           | --verbose           | verbose mode                                                                  | False                               |
+-----------+---------------------+-------------------------------------------------------------------------------+-------------------------------------+


nagios-plugins-argus.PEP.traffic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The response of the call executed by the status probe is parsed. The
value for the current number of requests executed and the number how
many of them have been successful is read out and compared to the same
numbers of the former test executed. Out of this the requests per
second, the completed requests per second and the erroneous requests per
second are computed and written into the Performance Data tab of Nagios.
Hence it can be displayed as a graph over time by Nagios add-ons, e.g.
pnp. Additionally the number of requests since the last test is written
into the Status information field of the Nagios control panel.

Options
'''''''

+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
| Mandatory | CLI option            | Description                                                                   | Default                                                      |
+===========+=======================+===============================================================================+==============================================================+
| YES       | --hostname=HOSTNAME   | The hostname of the service                                                   |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --cert CERT           | The SSL client certificate                                                    | ``/etc/grid-security/hostcert.pem``                          |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --key KEY             | The private key (the key must be unencrypted)                                 | ``/etc/grid-security/hostkey.pem``                           |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --capath CAPATH       | The directory where trust anchors are stored on the system                    | ``/etc/grid-security/certificates``                          |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --tempdir=TEMP\_DIR   | Storage path for the needed temporary file                                    | ``../../../../var/lib/grid-monitoring/nagios-plugins-argus`` |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --tempfile=TEMP\_FILE | Name for the needed temporary file                                            | hostname.nagios-plugins-argus.PDP.traffic.pickle             |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --help                | show this help message and exit                                               |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --port PORT           | The port of the service                                                       | ``8152``                                                     |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --url URL             | The status endpoint URL of the service, e.g. ``https://hostname:port/status`` |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --version             | show program's version number and exit                                        |                                                              |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --timeout TIMEOUT     | The TCP timeout for the HTTPS connection in seconds                           | ``20``                                                       |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+
|           | --verbose             | verbose mode                                                                  | False                                                        |
+-----------+-----------------------+-------------------------------------------------------------------------------+--------------------------------------------------------------+

