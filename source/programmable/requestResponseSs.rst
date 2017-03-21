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


Full code sample:-

==================== ===================
`Manual Fill cpp`_   `Manual Fill cs`_ 
-------------------- -------------------
`Manual Fill java`_  `Manual Fill py`_
==================== ===================

.. _Manual Fill cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ManualFill.cpp

.. _Manual Fill cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/ManualFill.cs

.. _Manual Fill java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/ManualFill.java

.. _Manual Fill py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/ManualFill.py


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python
	
	# ManualFill.py




Sell Side Ack Request
=====================


Full code sample:-

=====================  ===================
`Sell Side Ack cpp`_   `Sell Side Ack cs`_ 
---------------------  -------------------
`Sell Side Ack java`_  `Sell Side Ack py`_
=====================  ===================

.. _Sell Side Ack cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideAck.cpp

.. _Sell Side Ack cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideAck.cs

.. _Sell Side Ack java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideAck.java

.. _Sell Side Ack py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideAck.py


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python

	# SellSideAck.py



Sell Side Reject Request
========================


Full code sample:-

===================== ===================
`Sell Side Rej cpp`_  `Sell Side Rej cs`_ 
--------------------- -------------------
`Sell Side Rej java`_ `Sell Side Rej py`_
===================== ===================

.. _Sell Side Rej cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideRej.cpp

.. _Sell Side Rej cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideRej.cs

.. _Sell Side Rej java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideRej.java

.. _Sell Side Rej py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideRej.py


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python

	SellSideRej.py




