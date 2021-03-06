.. _argus_pepc_api:

Argus PEP Client Library: C API
===============================

Documentation
-------------

The Doxygen documentation for the Argus PEP client library describes the
C API and have an example.

API: http://argus-authz.github.com/argus-pep-api-c/doc/modules.html

PEP XACML Object Model
----------------------


.. figure:: /images/Argus_PEP_ObjectModel.png
   :align: center
   :scale: 25%


Basic Example
-------------

Basically, to use the Argus PEP client API, you will have to the following steps.

First, import the header with

.. code-block:: C

      #include "argus/pep.h"

Create and initialize the PEP client handle with

.. code-block:: C

   PEP * pep = pep_initialize()

Set the PEP Server URL with

.. code-block:: C

   pep_setoption(pep,PEP_OPTION_ENDPOINT_URL,"https://pepd.example.org:8154/authz")

If the PEP Server URL is protected by HTTPS with client authentication (the default), you must also configure
the client certificate or proxy with

.. code-block:: C

   pep_setoption(pep,PEP_OPTION_ENDPOINT_CLIENT_CERT,"/tmp/x509up_u500")

The client private key or proxy key with

.. code-block:: C

   pep_setoption(pep,PEP_OPTION_ENDPOINT_CLIENT_KEY,"/tmp/x509up_u500")

And the server CA trust anchors path with

.. code-block:: C

   pep_setoption(pep,PEP_OPTION_ENDPOINT_SERVER_CAPATH,"/etc/grid-security/certificates")

Optionally, you can register some
`Policy Information Points (PIP) <http://argus-authz.github.com/argus-pep-api-c/doc/group___p_i_p.html>`__
and
`Obligation Handlers (OH) <http://argus-authz.github.com/argus-pep-api-c/doc/group___obligation_handler.html>`__
of your own with

.. code-block:: C

   pep_addpip(...)

and

.. code-block:: C

   pep_addobligationhandler(...)

Create a XACML Request and add the required Subject, Resource, Action
and Environment to it with

.. code-block:: C

   xacml_request_create()
   xacml_request_addsubject(request,subject)

and so on.
See the
`PEP XACML Object Model <http://argus-authz.github.com/argus-pep-api-c/doc/org.glite.authz.pep-api-c/group___x_a_c_m_l.html>`__
for the complete API.

Submit the request and get the response:

.. code-block:: C

   pep_authorize(pep,&request,&response)

Process the response (if not already done by your obligation handlers).
Release the PEP client handle with

.. code-block:: C

   pep_destroy(pep)


Complex Example
---------------

A more detailed PEP client example is available
http://argus-authz.github.com/argus-pep-api-c/doc/pep_client_example_8c-example.html

Multi-threaded Programming
--------------------------

The Argus PEP client library is thread-friendly, but you are not allowed
to share a PEP handle among multiple threads.

Each thread have to create its own PEP handle:

.. code-block:: C

    /* Each thread creates its own PEP handle */
    PEP * pep= pep_initialize();

Within a thread you can reuse the PEP handle (multiple ``pep_authorize(..)`` calls).

If your threads are object (OO programming, ...), it is recommended you
to create (``pep_initialize``) the PEP handle in the constructor, and
release it (``pep_destroy``) in the destructor.


Processing Authorization Decision
---------------------------------

The PEP client MUST abide by the authorization decision as described in
here:

-  If the decision is ``Permit``, then the PEP client SHALL permit
   access. If **obligations** accompany the decision, then the PEP
   client SHALL permit access **only if it understands and it can and will enforce** those obligations.
-  If the decision is ``Deny``, then the PEP client SHALL deny access.
-  If the decision is ``NotApplicable``, meaning that no policy apply,
   then the PEP client SHALL deny access.
-  If the decision is ``Inderterminate``, then the PEP client SHALL deny
   access. The decision status message and status code should be used to
   produce an error message. Example:

.. code-block:: C

    ...
    xacml_result_t * result= xacml_response_getresult(response,i);
    fprintf(stdout,"response.result[%d].decision= %s\n", i, decision_tostring(xacml_result_getdecision(result)));
    fprintf(stdout,"response.result[%d].resourceid= %s\n", i, xacml_result_getresourceid(result));
    if (xacml_result_getdecision(result) == XACML_DECISION_INDETERMINATE) {
       xacml_status_t * status= xacml_result_getstatus(result);
       fprintf(stdout,"response.result[%d].status.message= %s\n", i, xacml_status_getmessage(status));
       statuscode= xacml_status_getcode(status);
       fprintf(stdout,"response.result[%d].status.code.value= %s\n", i, xacml_statuscode_getvalue(statuscode));
    }
    ...

