%META:TOPICINFO{author="mischa\_2esalle\_40cern\_2ech" date="1346071046"
format="1.1" version="1.12"}%
%META:TOPICPARENT{name="AuthZPEPgLExecInstall"}%

THIS PAGE IS OUTDATED.
======================

**Please go to the new `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__**

-- Main.OscarKoeroo - 23-Oct-2009

Argus: gLExec with LCMAPS C PEP Plug-in: Configuration
======================================================

Manual Configuration
====================

To configure gLExec to use the LCMAPS C PEP plug-in, you'll need to know
the PEPd endpoint information, have gLExec, LCAS, LCMAPS and the
required plug-ins installed, know which type of resource you are
installing it on (typically a Worker Node at the moment of this page's
creation) and what type of action is to be authorized (typically for
gLExec that is 'execute-now' or maybe 'queue' in the future).

gLExec Configuration file and its restrictions
----------------------------------------------

The gLExec configuration file describes how the gLExec executable must
do its work. There are strict rules about the location and permissions
set on the gLExec configuration file.

#. The gLExec configuration file MUST be located on the file system at
   the location specified at the software build time. This is known to
   be a very strict rule, but serves the purpose to prevent several file
   hijacking attacks.
#. The default gLite shipped version of gLExec will use the file path
   **``/opt/glite/etc/glexec.conf``**
#. The default VDT shipped version of gLExec will use the file path
   **``/etc/glexec.conf``**
#. The complete path MUST be **root** owned.
#. The **glexec.conf** file MUST be **root** owned
#. The file permissions on the glexec.conf must not exceed **0644**
   **``(-rw-r--r--)``**. They can not be lower then **0440**
   **``(-r--r-----)``**.
#. It is preferred to set the permissions with **0640**
   **``(-rw-r-----)``** or 0440 **``(-r--r-----)``** owned by
   **``root:root``**.

Workable example gLExec Configuration file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As the gLExec configuration file describes how the gLExec executable
must do its work, there are a lot of settings available to be able to
fulfill various operationally different requirement of sites. Here is a
brief list of options that the gLExec configuration file covers and a
complete example of such configuration file:

-  Write to a file or syslog
-  Describes the loglevel for all the logfiles
-  Dictates what kind of input and output file locking must be used (for
   concurrent scenarios)
-  Whitelists which individual account (for admins) or pool of accounts
   (production/pilot job submitter pools) is able to access and use
   gLExec.
-  Declare the initialization vectors for both LCAS and LCMAPS

Here is a workable example of the gLExec configuration file located at
**``/opt/glite/etc/glexec.conf``**:

::

    #
    #  Glexec (Generic wLCG)
    #
    [glexec]
    log_destination              = syslog
    #log_destination              = file
    #log_file                     = /var/log/glexec/glexec.log
    log_level                    = 1

    linger                       = yes
    silent_logging               = no
    user_white_list              = .atlasprod,.lhcbprod,okoeroo,msalle

    user_identity_switch_by      = lcmaps

    prohibit_exec_via_symlink    = no
    pedantic_security_checks     = no

    # You can use fcntl, flock or none
    input_lock_mechanism         = flock
    target_lock_mechanism        = flock

    lcas_db_file                 = /opt/glite/etc/lcas/lcas-glexec.db
    lcas_log_file                = /var/log/glexec/lcas_lcmaps.log
    lcas_debug_level             = 0
    lcas_log_level               = 0

    lcmaps_db_file               = /opt/glite/etc/lcmaps/lcmaps-glexec.db
    #lcmaps_get_account_policy    = pep_c_posix
    #lcmaps_get_account_policy    = scas_posix
    lcmaps_log_file              = /var/log/glexec/lcas_lcmaps.log
    lcmaps_debug_level           = 0
    lcmaps_log_level             = 0

The LCAS configuration file
---------------------------

In the Argus deployment scenario (similar to the SCAS deployment
scenario) the LCAS framework is redundant. In future releases of gLExec
the LCAS framework can be switched off and in a later stage complete be
removed from the system. For that, a little rework in gLExec is
required. This will be announced publicly and also written here.

The easiest solution is to configure the **``userban``** LCAS plug-in to
look at an empty file. The file must exist and by default should be
located at the path **``/opt/glite/etc/lcas/ban_users.db``**.

Workable example LCAS Configuration file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here is a workable example of the LCAS configuration, conforming the
glexec.conf example, located at
**``/opt/glite/etc/lcas/lcas-glexec.db``**:

#AnchorLCASConf

::

    # LCAS policy file/plugin definition
    # Written by: Oscar Koeroo - okoeroo * at * nikhef * dot * nl
    pluginname=lcas_userban.mod,pluginargs=ban_users.db 

The LCMAPS configuration file
-----------------------------

The gLExec, and also SCAS and the lcas-lcmaps-gt4-interface, form a
front for LCMAPS which abstracts the method and techniques used to
procure an account.

We'll dive into the Argus scenario in detail with the LCMAPS C PEP
Plug-in, it configuration options and what do setup around it to let it
work properly.

LCMAPS C PEP Plug-in Configuration Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This describes a list of options for to be passed on to the LCMAPS C PEP
Plug-in via the LCMAPS configuration file.

+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| Option                        | Description                                                                                                                                                      | Mandatory?    | Valid Input                                                                          | Example                                                                                                          |
+===============================+==================================================================================================================================================================+===============+======================================================================================+==================================================================================================================+
| ``pep-daemon-endpoint-url``   | The endpoint URL of the PEP daemon. This directive can be used multiple times for failover (in order)                                                            | Yes           | An URL (max. 256 chars)                                                              | ``--pep-daemon-endpoint-url`` ``https://pepd.example.org:8154/authz``                                            |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``resourceid``                | XACML request resource-id value\*                                                                                                                                | Yes, XOR(1)   | An URI (max 512 chars)                                                               | ``--resourceid`` ``http://authz-interop.org/xacml/resource/resource-type/wn``                                    |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``resourcetype``              | XACML request resource-id value (short notation). Set the XACML request resource-id value to ``http://authz-interop.org/xacml/resource/resource-type/<value>``   | Yes, XOR(1)   | ``se``, ``ce``, ``rb``, ``wn``                                                       | ``--resourcetype`` ``wn``                                                                                        |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``actionid``                  | XACML request action-id value\*                                                                                                                                  | Yes, XOR(2)   | An URI (max 512 chars)                                                               | ``--actionid`` ``http://glite.org/xacml/action/execute``                                                         |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``actiontype``                | XACML request action-id value (short notation). Set the XACML request action-id value to ``http://authz-interop.org/xacml/action/action-type/<value>``           | Yes, XOR(2)   | ``execute-now``, ``queue``, ``access-file``                                          | ``--actiontype`` ``execute-now``                                                                                 |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``check-certificates``        | force the PEP-C library to do SSL validation checks, regardless of its default setting                                                                           | No            | n/a                                                                                  | ``--check-certificates``                                                                                         |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``no-check-certificates``     | force the PEP-C library to ignore SSL validation checks                                                                                                          | No            | n/a                                                                                  | ``--no-check-certificates``                                                                                      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``capath``                    | Sets the CA path to the directory with CA certificates and CRL files                                                                                             | No            | A path, it's max. length is equal to the system limit                                | ``--capath /etc/grid-security/certificates/``                                                                    |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``cafile``                    | A file with a CA certificate or set of certificates                                                                                                              | No            | A file path, it's max. length is equal to the system limit                           | ``--cafile /etc/grid-security/certificate/123dasd.0``                                                            |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``pep-certificate-mode``      | Use implicit user proxy, explicit file or no client certificate at all                                                                                           | No            | ``implicit``, ``explicit``, ``none``                                                 | ``--pep-certificate-mode implicit``                                                                              |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``certificate``               | Certificate to use to contact PEPd                                                                                                                               | No            | A file path, it's max. length is equal to the system limit                           | ``--certificate /etc/grid-security/hostcert.pem``                                                                |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``key``                       | Private key file used to contact PEPd                                                                                                                            | No            | A file path, it's max. length is equal to the system limit                           | ``--key /etc/grid-security/hostkey.pem``                                                                         |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``pass``                      | Password for the certificate's private key file                                                                                                                  | No            | A password string                                                                    | ``--pass supersecretpassword``                                                                                   |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``passfile``                  | Password file for the certificate private key                                                                                                                    | No            | A file path, it's max. length is equal to the system limit                           | ``--passfile /root/secretpassfile``                                                                              |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``pep-c-debug``               | Enabled verbose logging in the libpepc library                                                                                                                   | No            | n/a                                                                                  | ``--pep-c-debug``                                                                                                |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| ``profile``                   | Selects the attribute profile in which the attributes are sent (both can be received and processed)                                                              | No            | http://authz-interop.org/profile/1.1 or http://glite.org/xacml/profile/grid-wn/1.0   | ``--profile`` http://authz-interop.org/profile/1.1 or ``--profile`` http://glite.org/xacml/profile/grid-wn/1.0   |
+-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+--------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+

\* The valid XACML values for the ``resourceid`` and ``actionid`` are
defined in the document `XACML Grid Worker Node Authorization Profile,
Version 1.0 <https://edms.cern.ch/document/1058175>`__ for the new
default profile. The previously used profile is defined in the document
`An XACML Attribute and Obligation Profile for Authorization
Interoperability in Grids <https://edms.cern.ch/document/929867>`__.

Workable example LCMAPS Configuration file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here is a workable example of the LCMAPS configuration, conforming the
glexec.conf example, located at
**``/opt/glite/etc/lcmaps/lcmaps-glexec.db``**:

#AnchorLCMAPSConf

::

    # LCMAPS policy file/plugin definition
    # Written by: Oscar Koeroo - okoeroo * at * nikhef * dot * nl
    # The Argus LCMAPS C PEP configuration using the Grid WN profile

    # default path for the 32bits modules (i386)
    #path = /opt/glite/lib/modules
    # path for the 64bits modules (x86_64)
    path = /opt/glite/lib64/modules

    # Plugin definitions:
    posix_enf        = "lcmaps_posix_enf.mod"
                       " -maxuid 1"
                       " -maxpgid 1"
                       " -maxsgid 32"

    verifyproxy = "lcmaps_verify_proxy.mod"
                 " -certdir /etc/grid-security/certificates"

    pepc        = "lcmaps_c_pep.mod"
                  "--pep-daemon-endpoint-url https://argus.example.org:8154/authz"
                  "--resourceid http://authz-interop.org/xacml/resource/resource-type/wn"
                  "--actionid http://glite.org/xacml/action/execute"
                  "--capath /etc/grid-security/certificates"
                  "--pep-certificate-mode implicit"
    #            "--pep-c-debug"

    # LCMAPS Execution Policies:
    argus:
    verifyproxy -> pepc
    pepc -> posix_enf 

Troubleshooting
===============

For information about troubleshooting, extracting useful information out
of the logfiles, where to find them and recovering from non-functional
state, we'd like to forward you to the `gLExec with LCMAPS C PEP Plug-in
Troubleshooting guide <AuthZPEPgLExecTroubleshooting>`__.
