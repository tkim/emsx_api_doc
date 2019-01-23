##################################
Sell-Side Request/Response Service
##################################
The sell-side Request/Response service is specifically used for EMSX to EMSX (E2E) setting where the sell-side EMSX is used to capture order flow from other buy-side EMSX users.

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


Manaul Fill Request
===================
The ``ManualFill`` request can be used on the sell-side EMSX<GO> settings to create fills and notifies EMSX<GO>.


Full code sample:-

==================== =================== ==================
`Manual Fill cpp`_   `Manual Fill cs`_   `Manual Fill vba`_    
-------------------- ------------------- ------------------
`Manual Fill java`_  `Manual Fill py`_ 
==================== =================== ==================

.. _Manual Fill cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ManualFill.cpp

.. _Manual Fill cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C#/ManualFill.cs

.. _Manual Fill java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/ManualFill.java

.. _Manual Fill py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/ManualFill.py

.. _Manual Fill vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/ManualFill.cls


.. hint:: 

    Please right click on the top code sample link to open in a new tab.


.. code-block:: python
   :linenos:    

    def processServiceStatusEvent(self,event,session):
        print "Processing SERVICE_STATUS event"
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print "Service opened..."

                service = session.getService(d_service)
    
                request = service.createRequest("ManualFill");

                #request.set("EMSX_REQUEST_SEQ", 1)

                request.set("EMSX_TRADER_UUID", 12109783)

                routeToFill = request.getElement("ROUTE_TO_FILL")
                    
                routeToFill.setElement("EMSX_SEQUENCE", 1234567)
                routeToFill.setElement("EMSX_ROUTE_ID", 1)
                    
                fills = request.getElement("FILLS")
                    
                fills.setElement("EMSX_FILL_AMOUNT", 1000)
                fills.setElement("EMSX_FILL_PRICE", 123.4)
                fills.setElement("EMSX_LAST_MARKET", "XLON")
                    
                fills.setElement("EMSX_INDIA_EXCHANGE","BGL")
                    
                fillDateTime = fills.getElement("EMSX_FILL_DATE_TIME")
                    
                fillDateTime.setChoice("Legacy");
                    
                fillDateTime.setElement("EMSX_FILL_DATE",20172203)
                fillDateTime.setElement("EMSX_FILL_TIME",17054)
                fillDateTime.setElement("EMSX_FILL_TIME_FORMAT","SecondsFromMidnight")

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        



Sell Side Ack Request
=====================
The SellSideAck request is used on the sell-side EMSX<GO> settings to create Ack message on incoming orders from 
buy-side EMSX<GO>. 


Full code sample:-

====================== =================== ====================
`Sell Side Ack cpp`_   `Sell Side Ack cs`_ `Sell Side Ack vba`_  
---------------------- ------------------- --------------------
`Sell Side Ack java`_  `Sell Side Ack py`_ 
====================== =================== ====================

.. _Sell Side Ack cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideAck.cpp

.. _Sell Side Ack cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideAck.cs

.. _Sell Side Ack java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideAck.java

.. _Sell Side Ack py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideAck.py

.. _Sell Side Ack vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/SellSideAck.cls


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python
   :linenos:
	
    def processServiceStatusEvent(self,event,session):
        print "Processing SERVICE_STATUS event"
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print "Service opened..."

                service = session.getService(d_service)
    
                request = service.createRequest("SellSideAck");

                #request.set("EMSX_REQUEST_SEQ", 1)

                request.setElement("EMSX_SEQUENCE", 1234567)
                    
                # If performing the ack on an order owned by another team member, provide owner's UUID
                #request.set("EMSX_TRADER_UUID", 7654321)

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"      


Sell Side Reject Request
========================
The SellSideReject request is used on the sell-side EMSX<GO> settings to create Reject message on incoming orders from 
buy-side EMSX<GO>. 


Full code sample:-

======================== ====================== =======================
`Sell Side Reject cpp`_  `Sell Side Reject cs`_ `Sell Side Reject vba`_ 
------------------------ ---------------------- -----------------------
`Sell Side Reject java`_ `Sell Side Reject py`_
======================== ====================== =======================

.. _Sell Side Reject cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/SellSideReject.cpp

.. _Sell Side Reject cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/SellSideReject.cs

.. _Sell Side Reject java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/SellSideReject.java

.. _Sell Side Reject py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/SellSideReject.py

.. _Sell Side Reject vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/SellSideReject.cls


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python
   :linenos:
	
    def processServiceStatusEvent(self,event,session):
        print "Processing SERVICE_STATUS event"
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print "Service opened..."

                service = session.getService(d_service)
    
                request = service.createRequest("SellSideReject");

                #request.set("EMSX_REQUEST_SEQ", 1)

                request.setElement("EMSX_SEQUENCE", 1234567)
                    
                # If performing the reject on an order owned by another team member, provide owner's UUID
                #request.set("EMSX_TRADER_UUID", 7654321)

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


