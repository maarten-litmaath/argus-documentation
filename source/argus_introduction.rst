.. _argus_introduction:

Argus Authorization Service
===========================

Summary
-------

The Argus Authorization Service renders consistent authorization
decisions for distributed services (e.g., user interfaces, portals,
computing elements, storage elements). The service is based on the XACML
standard, and uses authorization policies to determine if a user is
allowed or denied to perform a certain action on a particular service.

The Argus Authorization Service is composed of three main components:

-  The Policy Administration Point (PAP) provides the tools to author
   authorization policies, organize them in the local repository and
   configure policy distribution among remote PAPs.
-  The Policy Decision Point (PDP) implements the authorization engine,
   and is responsible for the evaluation of the authorization requests
   against the XACML policies retrieved from the PAP.
-  The Policy Enforcement Point Server (PEP Server) ensures the
   integrity and consistency of the authorization requests received from
   the PEP clients. Lightweight PEP client libraries are also provided
   to ease the integration and interoperability with other EMI services
   or components.

The following graphic shows the interaction between the components of
the service:

.. figure:: /images/ARGUS_components.png
   :align: center

   **Argus service components**

**Note:** In Argus, the PEP is separated in a client/server
architecture. The PEP Server handles the lightweight PEP client
requests, and runs on the Argus node.

Argus Service Installation
--------------------------

The following section provides instructions for setting up an Argus
environment quickly. It does not provide an exhaustive description of
every possible deployment model or configuration option, that can be
found in the following :ref:`Service Components <argus_intro_service_components>` and
:ref:`Enabled Applications <argus_intro_enabled_applications>` sections.

Before you continue it is recommend that you read this
:ref:`introduction to the Argus system <argus_concepts>`. This will provide you with a better
understanding of how the components work together, what information
passes between the components and how policies are formed.

Argus Deployment
~~~~~~~~~~~~~~~~

The Argus Service is installed with YUM.
For the version 1.7, there isn't an automatic configuration tool.
Older version are configured with YAIM.

Please follow the Argus :ref:`Deployment for version 1.7 <argus-deployment>`
documentation.

gLExec Worker Node with Argus Deployment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To install and configure an Argus compatible gLExec worker node, follow
these `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__

.. _argus_intro_service_components:

Service Components
------------------

If you are beginning to install the authorization service from scratch,
you should install the components in the order listed here; PAP, then
PDP, then PEPd. You don't have to, but it makes the most sense for most
use cases.

PAP: Policy Administration Point
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The :ref:`Policy Administration Point <argus_pap_index>` (PAP) provides three major functions:

-  Provide the tools for authoring policies
-  Store and manage authored policies
-  Provide managed policies to other authorization service components

.. list-table::
   :header-rows: 0

   *
      - :ref:`Installation <argus_pap_installation>`
      - :ref:`Configuration <argus_pap_configuration>`
      - :ref:`Operation <argus_pap_operation>`


PDP: Policy Decision Point
~~~~~~~~~~~~~~~~~~~~~~~~~~

The :ref:`Policy Decision Point <argus_pdp_index>` (PDP) is a policy evaluation engine. The PDP
receives authorization requests from Policy Enforcement Points and
evaluates these requests against authorization policies retrieved from
the PAP.

.. list-table::
   :header-rows: 0

   *
      - :ref:`Installation <argus_pdp_installation>`
      - :ref:`Configuration <argus_pdp_configuration>`
      - :ref:`Operation <argus_pdp_operation>`
      - :ref:`Troubleshooting <argus_pdp_troubleshooting>`


.. _argus_introduction_pep:

PEP: Policy Enforcement Point
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The :ref:`Policy Enforcement Point <argus_pep_index>` (PEP) is the client to the authorization
service. It gathers information relevant to an authorization request
(e.g. who the user, what action they are attempting to perform, which
service they are attempting to perform the action on, etc.) and sends
the request to the PDP for evaluation. The PEP then acts upon returned
result by allowing the request to proceed (in the case a positive
authorization decision) or by denying the action (in the event of a
negative decision).

In Argus, the PEP itself has a client/server architecture

  - The PEP Server handles the lightweight PEP client requests, and runs on the
    Argus node.

  - Lightweight PEP client libraries are available to authorize
    requests from the application side, and to enforce decision locally. There are
    two variants available:

    - PEP Client C API: :ref:`Programming Interface (API) <argus_pepc_api>`
    - PEP Client Java API: :ref:`Programming Interface (API) <argus_pepj_api>`


.. list-table::
   :header-rows: 0

   *
      - :ref:`Installation <argus_pepd_installation>`
      - :ref:`Configuration <argus_pepd_configuration>`
      - :ref:`Operation <argus_pepd_operation>`
      - :ref:`Troubleshooting <argus_pepd_troubleshooting>`


:ref:`PEP command-line clients <argus_pepc_cli>` are also available

.. _argus_intro_enabled_applications:

Enabled Applications
--------------------

The following applications contain an Argus PEP client and can make
authorization requests to the Argus service.

.. list-table:: Argus-enabled Applications
   :header-rows: 0
   :stub-columns: 1

   *
     - GSI PEP Callout
     - :ref:`Introduction <gsi_pep_callout>`
     - :ref:`Installation <pep_gsi_callout_installation>`
     - :ref:`Configuration <pep_gsi_callout_configuration>`
     - :ref:`Troubleshooting <pep_gsi_callout_troubleshooting>`

   *
     - gLExec with PEP Plugin
     - `Introduction <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Introduction>`__
     - `Installation <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Package_installation>`__
     - `Configuration <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Manual_configuration>`__
     - `Troubleshooting <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Debugging_hints>`__


Support and Monitoring
----------------------

GGUS Support
~~~~~~~~~~~~

General support (installation, site administrator) for Argus is
available through `GGUS <https://ggus.eu>`__

Argus Support Mailing List
~~~~~~~~~~~~~~~~~~~~~~~~~~

Argus specific (developer, site administrator) questions can be sent
directly to the argus-support@googlegroups.com `mailing
list <https://groups.google.com/d/forum/argus-support>`__. You don't
need a Google email address or a Google account to send or receive
emails from this mailing list.

-  **Subscription:** To subscribe to the support mailing list, simply
   send an email to: argus-support+subscribe@googlegroups.com
-  **Unsubscribe:** You can unsubscribe from the list at anytime by
   sending an email to: argus-support+unsubscribe@googlegroups.com

**NOTE:** The mailing list was previously argus-support@cern.ch, but it
have been migrated to argus-support@googlegroups.com at the end of the
EMI project (April 2013).

Nagios Monitoring
~~~~~~~~~~~~~~~~~

Nagios plugins are available to monitor an Argus server.

-  **UMD**: :ref:`Argus Nagios Probes Documentation <argus_emi_nagios_probes>`

Development Information
-----------------------

Argus is an open-source product hosted on `GitHub <https://github.com/argus-authz>`__. In addition to this
user-oriented documentation, you can find a description of Argus architecture and main components at Argus
`main site <http://argus-authz.github.io>`__.

Specifications
~~~~~~~~~~~~~~

-  XACML 2.0 Specifications `eXtensible Access Control Markup Language (XACML) Version 2.0 <http://docs.oasis-open.org/xacml/2.0/access_control-xacml-2.0-core-spec-os.pdf>`__
-  `SAML 2.0 Profile of XACML, Version 2 (Working Draft 5) <http://www.oasis-open.org/committees/download.php/24681/xacml-profile-saml2.0-v2-spec-wd-5-en.pdf>`__
-  XACML Profile for the gLite WN `XACML Grid Worker Node Authorization Profile (v. 1.0) <https://edms.cern.ch/document/1058175>`__
-  XACML Profile for the gLite CE `XACML Grid Computing Element Authorization Profile (v. 1.0) <https://edms.cern.ch/document/1078881/>`__
-  XACML Authorization Profile for EMI `Common XACML Authorization Profile (v. 1.1) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1>`__

Requirements
~~~~~~~~~~~~

-  `Requirements Document <https://edms.cern.ch/document/944192>`__
   *(EDMS document 944192)*
-  `Testing Plan <https://edms.cern.ch/document/986067>`__ *(EDMS
   document 986067)*

Presentations
~~~~~~~~~~~~~

-  :download:`Argus - EMI Authorization Integration <docs/20110531-EMI_AllHands_2011-Argus_Integration.ppt>` (EMI AH 2011, 31 May 2011, Lund)
-  :download:`Argus - EMI Authorization Service <docs/20110412-EGI_UF_2011-Argus.ppt>` (EGI UF 2011, 12 April 2011, Vilnus)
-  :download:`Argus Security <docs/20100917_EGI-TF_ArgusSecurity.ppt>` (EGI TF 2010 Security Session, 17 Sept. 2010, Amsterdam)
-  :download:`General introduction to the authorization service <docs/introduction_authz_service.ppt>`
-  :download:`Introduction to Argus for ROD <docs/100602_argus_intro_rod.ppt>` (EGI ROD Workshop, 2 June 2010, Amsterdam)

Souce Code Information
~~~~~~~~~~~~~~~~~~~~~~

The Argus source code is on `GitHub <http://github.com>`__.

-  Argus Authorization Service: https://github.com/argus-authz


Development Tools
~~~~~~~~~~~~~~~~~

The Argus PT uses the following :ref:`development tools <argus-development-tools>`.

For performance and load testing we use the following :ref:`testing suite <argus-load-lifetime-testing>`.

Argus Production Settings and Optimization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Production sites can optimize the Argus Service settings to their
specific needs. Please have a look at the :ref:`Argus Service Fine Tuning <argus-emi-fine-tuning>` documentation.

Perfomance and Load Testing
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Argus provides a load testsuite, based on Grinder framework.

More information can be found on `Argus load testsuite <https://github.com/argus-authz/load-testsuite>`__.


Additional Support
------------------

-  `HERAS-AF <http://www.herasaf.org/index.php>`__ project has supported
   the project by providing a good XACML policy engine and excellent,
   ongoing, support of their code.
-  YourKit is kindly supporting this open source projects with its
   full-featured Java Profiler. YourKit, LLC is the creator of
   innovative and intelligent tools for profiling Java and .NET
   applications. Take a look at YourKit's leading software products:
   `YourKit Java Profiler <http://www.yourkit.com/java/profiler/index.jsp>`__ and
   `YourKit .NET Profiler <http://www.yourkit.com/.net/profiler/index.jsp>`__.
-  This product includes software developed by the `Caucho Technology <http://www.caucho.com/>`__.

About the name Argus
--------------------

In Greek mythology Argus was a 100-eyed giant that was meant to watch
and protect various things and people including the Goddess Io. He was
slain by Hermes but the gods chose to preserve his hundred eyes and
affix them to the tail-feathers of a brilliantly colored bird, the
peacock, in homage. The peacock logo is provided by the royalty free
clip art site `clker.com <http://www.clker.com>`__.
