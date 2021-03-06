%META:TOPICINFO{author="mischa\_2esalle\_40cern\_2ech" date="1346071000"
format="1.1" version="1.8"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

THIS PAGE IS OUTDATED.
======================

**Please go to the new `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__**

-- Main.OscarKoeroo - 22-Oct-2009

Argus: gLExec with Argus: Description
=====================================

Module Description
==================

gLExec is a program to make the required mapping between the grid world
and the Unix notion of users and groups, and has the capacity to enforce
that mapping by modifying the uid and gids of running processes. Based
on LCAS and LCMAPS, it can both act as a light-weight 'gatekeeper'
replacement, and even be used on the worker node in late-binding (pilot
job) scenarios.

The gLExec uses the LCMAPS framework to abstract different
implementations and techniques that can be implemented through the use
of specialized plug-ins. The plug-ins do the actual work (e.g. extensive
credentials, procuring an account locally or through a central
authorization service, like Argus).

The gLExec will interact with Argus using an LCMAPS plug-in which uses
the PEP-C library to communicate to it. The plug-in calls out to the
`Argus PEP Daemon <AuthorizationFramework#Policy_Enforcement_Point>`__.
The response from the Argus PEP Daemon is extracted and used to deny or
fulfill the mapping request to the resource.

More information about gLExec can be found on the `Nikhef GridWiki for
gLExec
page <https://www.nikhef.nl/pub/projects/grid/gridwiki/index.php/GLExec>`__
which includes specific information about batch-system handling, with
example scripts, the gLExec wrapper scripts and other gLExec related
information.

Focused towards system administrators, there is also a **Service
Reference Card** made available for `gLExec <GLExec>`__.

Authorization and Mapping
=========================

Based on the Grid credentials, LCMAPS C PEP plug-in will send an XACML
request to the `Argus PEP
Daemon <AuthorizationFramework#Policy_Enforcement_Point>`__, and then
parse the XACML response decision to authorize the user and the
obligations to map him to a local account which will effectively switch
the active Unix account of the running process.

XACML Request
-------------

The gLExec LCMAPS PEP plug-in sends an XACML request to the PEP Daemon
with the following elements by default\*:

-  XACML subject with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:subject:key-info``
   -  Value: The PEM encoded Grid credentials provided by the calling
      process, fetched from the file content located by the environment
      variable $GLEXEC\_USER\_CERT. The location to the proxy file of
      the pilot job payload owner (e.g. the end user) must be set by
      this variable in order for gLExec to pass it on. The mapping will
      be based on this information.

-  XACML resource with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``
   -  Value: The type of resource of the node and service from where the
      request originated (e.g. SE, CE, WN or RB/WMS).

-  XACML action with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:action:action-id``
   -  Value: The type of action to be taken on the node from where the
      request originated (e.g. access-file, queue, execute-now).

-  XACML environment with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:subject:key-info``
   -  Value: The PEM encoded Grid credentials provided by the calling
      process, fetched from the file content located by the environment
      variable $X509\_USER\_PROXY. The location to the proxy file of the
      pilot job executor or pilot job framework manager must be set by
      this variable in case of a pilot job framework scenario.

XACML Response
--------------

The PEP Daemon sends back a XACML response to the gLExec LCMAPS PEP
plug-in. The following XACML response elements are parsed to authorize
and map the user:

-  XACML decision element: Contains the **authorization decision**
   ``Permit``, ``Deny``, ``Indeterminate`` or ``NotApplicable``
-  XACML obligation, ObligationId:
   ``http://glite.org/xacml/obligation/local-environment-map/posix``,
   with the three possible attribute assignment elements:

   -  AttributeId: ``http://glite.org/xacml/attribute/user-id``
   -  Value: Contains the **Unix User ID** of the mapped user account in
      a string format.
   -  AttributeId: ``http://glite.org/xacml/attribute/group-id/primary``
   -  Value: Contains the **Unix Group ID** of the mapped primary group
      in a string format.
   -  AttributeId: ``http://glite.org/xacml/attribute/group-id``
   -  Value: Contains the **Unix Secondary Group ID** of the mapped
      group in a string format.

The local identity mapping will only succeed if the authorization
decision is ``Permit``.

\*: The previously used XACML attribute profile can still be selected,
but this needs to be explicitly configured.

Installation
============

Please move to the `gLExec with LCMAPS C PEP Plug-in installation
guide <AuthZPEPgLExecInstall>`__.
