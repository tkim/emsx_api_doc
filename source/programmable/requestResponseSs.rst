##################################
Sell-Side Request/Response Service
##################################


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

These are initialized in the constructor as below and are then available for the life of the application for 
submission of various requests. 

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


Full code sample:-

==================== ===================
    
-------------------- -------------------
`Manual Fill java`_  
==================== ===================

.. Manual Fill cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ManualFill.cpp

.. Manual Fill cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/ManualFill.cs

.. _Manual Fill java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/ManualFill.java

.. Manual Fill py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/ManualFill.py


.. hint:: 

    Please right click on the top code sample link to open in a new tab.


.. code-block:: python
    
    # ManualFill.py


Sell Side Ack Request
=====================


The SellSideAck request is used on the sell-side EMSX<GO> settings to create Ack message on incoming orders from 
buy-side EMSX<GO>. 


Full code sample:-

=======================  ===================
    
-----------------------  -------------------
`Sell Side Ack java`_  
=======================  ===================

.. Sell Side Ack cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideAck.cpp

.. Sell Side Ack cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideAck.cs

.. _Sell Side Ack java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideAck.java

.. Sell Side Ack py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideAck.py


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python

	# SellSideAck.py



Sell Side Reject Request
========================


The SellSideReject request is used on the sell-side EMSX<GO> settings to create Reject message on incoming orders from 
buy-side EMSX<GO>. 


Full code sample:-

======================== ======================
 
------------------------ ----------------------
`Sell Side Reject java`_ 
======================== ======================

.. Sell Side Reject cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideReject.cpp

.. Sell Side Reject cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideReject.cs

.. _Sell Side Reject java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideReject.java

.. Sell Side Reject py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideReject.py


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python

	# SellSideRej.py




