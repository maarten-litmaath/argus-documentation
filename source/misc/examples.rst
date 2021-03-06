.. _argus_request_and_policy_examples:

Example of Authorization Requests and Policies
==============================================

The following examples of policies and associated requests are meant to
illustrate some of the possible policies, requests, and resulting
authorization decisions.

Note, the examples below use the PAP's
:ref:`simplified policy language <argus_simplified_policy_language>` to express policies. So please
review this syntax, before proceeding, if you are not yet familiar with it.

User Based Authorization
------------------------

In this example one user is permitted, and one is denied, the ability to
submit jobs on the CERN CE resource. Each user is identified by a X.509
subject DN. This example also shows how identity-based access control is
simply a degenerate case of attribute-based access control.

Authorization Policy
^^^^^^^^^^^^^^^^^^^^

::

    resource "http://cern.ch/authz/ce1" {
       action "http://cern.ch/authz/actions/ce-submit" {
          rule permit {
             subject="CN=John Doe,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH"
          }
          rule deny {
             subject="CN=Jane Smith,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH"
          }
       }
    }

Requests
^^^^^^^^

A request of:

-  resource attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``,
      **value:** ``http://cern.ch/authz/ce1``
   -  any other attributes

-  action attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:action:action-id``,
      **value:** ``http://cern.ch/authz/actions/ce-submit``
   -  any other attributes

-  subject attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:subject:subject-id``,
      **value:**
      ``CN=John Doe,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH``

would result in an authorization decision of ``permit`` because the
given resource and actions IDs match what was given in the policy and
the subject ID DN matches what was given in the permit rule. If the
request contained any other resource, action, or subject attribute these
would be ignored since the policy does not reference them. If the
request had not contained the attributes listed it would have resulted
in an authorization decision of ``not applicable`` since no policy would
have matched the request.

A request of:

-  resource attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``,
      **value:** ``http://cern.ch/authz/ce1``
   -  any other attributes

-  action attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:action:action-id``,
      **value:** ``http://cern.ch/authz/actions/ce-submit``
   -  any other attributes

-  subject attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:subject:subject-id``,
      **value:**
      ``CN=Jane Smith,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH``

would result in an authorization decision of ``deny`` since the resource
and action IDs match and this time the subject ID matches the
requirement for the deny rule.

Note, in the second request, if the subject ID had contained at least
the values
``CN=John Doe ,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH``
and
``CN=Jane Smith,OU=Standard Commercial Certificate,O=Acme,L=Zuerich,ST=Zuerich,C=CH``
the authorization decision would have been ``permit`` since the permit
rule would have been evaluated first and would have returned the result.

Per-VO Pilot Job Authorization Policy
-------------------------------------

This example shows a policy that allows pilot jobs from one VO, atlas,
and denies pilot jobs from others. It also demonstrates rules that rely
on more than one attribute.

Authorization Policy
^^^^^^^^^^^^^^^^^^^^

::

    resource "http://cern.ch/authz/ce1" {
       action "http://cern.ch/authz/actions/ce-submit" {
          rule permit {
             vo="atlas"
             pilot-job="true"
          }
          rule deny {
             pilot-job="true"
          }
       }
    }

Requests
^^^^^^^^

A request of:

-  resource attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``,
      **value:** ``http://cern.ch/authz/ce1``
   -  any other attributes

-  action attributes:

   -  **id:** ``urn:oasis:names:tc:xacml:1.0:action:action-id``,
      **value:** ``http://cern.ch/authz/actions/ce-submit``
   -  any other attributes

-  subject attributes:

   -  **id:** ``http://authz-interop.org/xacml/subject/vo``, **value:**
      ``atlas``
   -  **id:** ``http://example.org/authz/attribute/pilot-job``,
      **value:** ``true``

would result in a decision of ``permit`` since the atlas VO is permitted
to submit pilot jobs. A request where the VO was ``lhcb`` or ``cms``
would result in a decision of ``deny`` since these VOs are not permitted
to submit jobs.

A request of: \* resource attributes:

-  **id:** ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``,
   **value:** ``http://cern.ch/authz/ce1``
-  any other attributes \* action attributes:
-  **id:** ``urn:oasis:names:tc:xacml:1.0:action:action-id``, **value:**
   ``http://cern.ch/authz/actions/ce-submit``
-  any other attributes \* subject attributes:
-  **id:** ``http://authz-interop.org/xacml/subject/vo``, **value:**
   ``atlas``

would result in a decision of ``not applicable`` since the current
policy indicates that only jobs from the VO atlas that are also pilot
jobs are permitted. Requests without a pilot-job attribute are not
addressed by this policy.
