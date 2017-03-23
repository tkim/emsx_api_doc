########################################
Sell-Side Request/Response Service (COM)
########################################


The EMSX API allows developers to use the Request/Response services for order and route creation, modification, 
queries related to orders and routes (placements) as well as EMSX Team details. Depending on the type of action 
required, the application programmer must create a specific request, populate it with required parameters and send 
that request to the EMSX API service, which provides the response. Communication with the request/response service 
requires the following steps:

	#. Create a session (if session does not yet exist).

	#. Connect session to ``//blp/emapisvc_beta`` or ``//blp/emapisvc`` service and start it.
	
	#. Fetch a service object from the session representing emapisvc.
	
	#.  Use the service object from above to create a Request object of the desired type
	
	#. Send request object via sendRequest method of session object, pass object of type EventQueue to the sendRequest.
	
	#. Loop through the EventQueue object until event of type ``Event::RESPONSE`` is read.

These are initialized in the constructor as below and are then available for the life of the application for submission 
of various requests. 

The sell-side Request/Response service is specifically used for EMSX to EMSX (E2E) setting where the sell-side EMSX is used to capture order flow from other buy-side EMSX users.


Description of Request/Response Service
=======================================


EMSX API supports the following Request/Response services:-

*Please note, the descriptions to the legacy request/response services are omitted from the description section.*


=================================== =================================================================
Request Name             			Action
=================================== =================================================================
``ManualFill``						Request to manually fill a child route.
``SellSideAck`` 					Request to acknowlede an order on EMSX to EMSX setting.
``SellSideReject`` 					Request to reject an order on EMSX to EMSX setting.
=================================== =================================================================


Manaul Fill Request
===================


The ``ManualFill`` request can be used on the sell-side EMSX<GO> settings to create fills and notifies EMSX<GO>.


.. code-block:: vb.net
	

Sell Side Ack Request
=====================


The SellSideAck request is used on the sell-side EMSX<GO> settings to create Ack message on incoming orders from 
buy-side EMSX<GO>. 


.. code-block:: vb.net
	
	
Sell Side Reject Request
========================


The SellSideReject request is used on the sell-side EMSX<GO> settings to create Reject message on incoming orders from 
buy-side EMSX<GO>.


.. code-block:: vb.net
	

