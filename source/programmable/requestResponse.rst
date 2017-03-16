#################################
Buy-Side Request/Response Service
#################################


The EMSX API allows developers to use the Request/Response services for order and route creation, modification, queries related to orders and routes (placements) as well as EMSX Team details. Depending on the type of action required, the application programmer must create a specific request, populate it with required parameters and send that request to the EMSX API service, which provides the response. Communication with the request/response service requires the following steps:

	#. Create a session (if session does not yet exist).

	#. Connect session to ``//blp/emapisvc_beta`` or ``//blp/emapisvc`` service and start it.
	
	#. Fetch a service object from the session representing emapisvc.
	
	#.  Use the service object from above to create a Request object of the desired type
	
	#. Send request object via sendRequest method of session object, pass object of type EventQueue to the sendRequest.
	
	#. Loop through the EventQueue object until event of type ``Event::RESPONSE`` is read.

These are initialized in the constructor as below and are then available for the life of the application for submission of various requests. 


Description of Request/Response Service
=======================================


EMSX API supports the following Request/Response services:-

*Please note, the descriptions to the legacy request/response services are omitted from the description section.*

=================================== =================================================================
Request Name             			Action
=================================== =================================================================
AssignTrader						Assign an order to another UUID
CancelRouteEX						Cancel outstanding routes (placements)
CreateOrder                     	Create an order or stage an order into EMSX<GO>
CreateOrderAndRouteEx				Create a new order and route in a single request. 
CreateOrderAndRouteManually	 		Create the order and notify EMSX this is routed.
DeleteOrder					 		Delete an existing order in EMSX<GO>
GetAllFieldMetaData			 		Get all field meta data in a response message
GetAssetClass						Get all asset class data in a response message
GetBrokers							Get all broker data in a response message
GetBrokerStrategies					Get all broker strategy data in a response message
GetBrokerStrategiesWithAssetClass 	Get all broker strategy information and asset class data
GetBrokerStrategyInfo 				Get all broker strategy info data in a response message
GetBrokerStrategyInfoWithAssetClass Get all broker strategy info and asset class data
GetBrokerWithAssetClass 			Get all broker data with asset class in a response message
GetFieldMetaData 					Get field meta data in a reponse message
GetTeams 							Get team data in a response message
GroupRouteEx 						Submit the entire list as a single route to a basket algorithm
ModifyOrder 						Modify parent order
ModifyRouteEx 						Modify child route
RouteEx 							Route existing order
RouteManuallyEx 					Route manually and notify EMSX that it is routed.
SellSideAck 						Request to acknowlede an order on EMSX to EMSX setting
SellSideReject 						Request to reject an order on EMSX to EMSX setting
=================================== =================================================================


.. note::

	``CreateOrderAndRouteEx`` can be used for both strategy and non-strategy broker destinations.

	``CreateOrderAndRouteManually`` is generally used for phone orders to brokers, where the actual placement is outside of EMSX<GO>.

	``RouteEx`` can be used for both strategy and non-strategy broker destinations.

	``RouteManuallyEx`` is generally used for phone orders to manually enter back the execution to EMSX<GO>.

	``SellSideAck`` is used for EMSX to EMSX or E2E settings where sell-side EMSX<GO> is used to receive order from buy-side EMSX.

	``SellSideReject`` is used for EMSX to EMSX or E2E settings where sell-side EMSX<GO> is used to receive order from buy-side EMSX.



CFD & Odd Lot Flag
==================


This is a feature that indicates CFD orders or to flag an odd lot in EMSX API.

``EMSX_CFD_FLAG``  is used to flag a particular order as CFD 

	* 0 = not flagged														
	* 1 = flagged															



``EMSX_ODD_LOT_FLAG``  is an odd lot is a quantity of stock that is less than 100 shares. A deal involving 100 shares or more is considered a round-lot transactions.

	* 0 = not an odd lot / it won't fill odd lots							
	* 1 = odd lot 															



Date & Time Format
==================


All date format except ``EMSX_QUEUED_TIME`` are in yyyymmdd format. All time format except ``EMSX_STRATEGY_END_TIME`` and ``EMSX_STRATEGY_START_TIME`` are in number of seconds from midnight.


=================================== =================================================================
Element								Description             		
=================================== =================================================================
``EMSX_DATE``						yyyymmdd
``EMSX_GTD_DATE``					yyyymmdd
``EMSX_LAST_FILL_DATE``				yyyymmdd
``EMSX_QUEUED_DATE``				yyyymmdd
``EMSX_ROUTE_CREATE_DATE``			yyyymmdd
``EMSX_SETTLE_DATE``				yyyymmdd
``EMSX_QUEUED_TIME``				hhmm
``EMSX_STRATEGY_END_TIME``			hhmmss
``EMSX_STRATEGY_START_TIME``		hhmmss
``EMSX_LAST_FILL_TIME``				Number of seconds from midnight
``EMSX_ROUTE_CREATE_TIME``			Number of seconds from midnight
``EMSX_ROUTE_LAST_UPDATE_TIME``		Number of seconds from midnight
``EMSX_TIME_STAMP``					Number of seconds from midnight
=================================== =================================================================

The ``//blp/emsx.history`` and ``//blp/emsx.history.uat`` are set in date time objects unlike the ``//blp/emapisvc`` or ``//blp/emapisvc_beta.``


Custome Notes & Free Text Fields
================================


The EMSX API provides several different EMSX options for entering and using free text fields. Some of these free text fields can be used for an internal only workflow where the others can be used to communicate with the various execution counterparts. 

The following elements are available on order and/or route subscription services. These elements will be passed to the external trading counterparts.


=================================== ==================================================================
Element								Description             		
=================================== ==================================================================
``EMSX_ACCOUNT``					30-character free text field (29+1 check digit), FIX Tag 1
``EMSX_NOTE``						44-character free text field (43+1 check digit), FIX Tag 58
``EMSX_ORDER_REF_ID``				16-character field (15+1 check digit) *order subscription only*
``EMSX_ROUTE_REF_ID``				16-character field (15+1 check digit) *route subscription only*
``EMSX_TRADER_NOTES``				44-character free text field (43+1 check digit), **internal only**
=================================== ==================================================================


The following elements are available only for internal fields unless custom mapped to a custom FIX tag to a particular trading counterparty. 


.. note:: 

	**The following elements are not available on either order or route subscription service.**


=================================== ==================================================================
Element								Description             		
=================================== ==================================================================
``EMSX_CUSTOM_NOTE1``				80-character free text field (79+1 check digit)
``EMSX_CUSTOM_NOTE2``				80-character free text field (79+1 check digit) 
``EMSX_CUSTOM_NOTE3``				80-character free text field (79+1 check digit) 
``EMSX_CUSTOM_NOTE4``				80-character free text field (79+1 check digit)
``EMSX_CUSTOM_NOTE5``				80-character free text field (79+1 check digit)
=================================== ==================================================================



Assign Trader Request
=====================


AssignTrader request allows EMSX API to reassign order to another user UUID. A typical setup will have the different UUID as another part of the TEAM setup for the order creater UUID. This will allow systematically generated trades to be reassigned to another human trader if need be from the EMSX API.

Assigned trader must be in same EMBR group for this to work. EMBR<GO> is an internal Bloomberg function the EMSX account managers will use to set this feature on behalf of the client. The EMSX account manager will check off the ability to reassign before the AssignTrader request will work. Once this feature is on, trading on behalf other UUID feature will no longer work for that team.

.. code-block:: python

	# AssignTrader.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	ASSIGN_TRADER           = blpapi.Name("AssignTrader")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	bEnd=False

	class SessionEventHandler():

	    def processEvent(self, event, session):
	        try:
	            if event.eventType() == blpapi.Event.SESSION_STATUS:
	                self.processSessionStatusEvent(event,session)
	            
	            elif event.eventType() == blpapi.Event.SERVICE_STATUS:
	                self.processServiceStatusEvent(event,session)

	            elif event.eventType() == blpapi.Event.RESPONSE:
	                self.processResponseEvent(event)
	            
	            else:
	                self.processMiscEvents(event)
	                
	        except blpapi.Exception as e:
	            print "Exception:  %s" % e.description()
	        return False


	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            else:
	                print msg
	                

	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."

	                service = session.getService(d_service)
	    
	                request = service.createRequest("AssignTrader")
	                
	                request.append("EMSX_SEQUENCE", 3744303)
	                request.append("EMSX_SEQUENCE", 3744341)

	                request.set("EMSX_ASSIGNEE_TRADER_UUID", 12109783)
	            
	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                
	    def processResponseEvent(self, event):
	        print "Processing RESPONSE event"
	        
	        for msg in event:
	            
	            print "MESSAGE: %s" % msg.toString()
	            print "CORRELATION ID: %d" % msg.correlationIds()[0].value()


	            if msg.correlationIds()[0].value() == self.requestID.value():
	                print "MESSAGE TYPE: %s" % msg.messageType()
	                
	                if msg.messageType() == ERROR_INFO:
	                    errorCode = msg.getElementAsInteger("ERROR_CODE")
	                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
	                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
	                elif msg.messageType() == ASSIGN_TRADER:
	                    success = msg.getElementAsBool("EMSX_ALL_SUCCESS")
	                    if success:
	                        print "All orders successfully assigned"
	                        successful = msg.getElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS") 
	                        
	                        if successful.numValues() > 0: print "Successful assignments:-"

	                        for order in successful.values():
	                            seq = order.getElement("EMSX_SEQUENCE").getValue()
	                            print seq

	                    else:
	                        print "One or more failed assignments...\n"
	                        
	                        if msg.hasElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS"):
	                            successful = msg.getElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS") 
	                            
	                            if successful.numValues() > 0: print "Successful assignments:-"

	                            for order in successful.values():
	                                seq = order.getElement("EMSX_SEQUENCE").getValue()
	                                print seq
	                                
	                            
	                        if msg.hasElement("EMSX_ASSIGN_TRADER_FAILED_ORDERS"):
	                            failed = msg.getElement("EMSX_ASSIGN_TRADER_FAILED_ORDERS")

	                            if failed.numValues() > 0: print "Failed assignments:-"

	                            for order in failed.values():
	                                seq = order.getElement("EMSX_SEQUENCE").getValue()
	                                print seq

	                global bEnd
	                bEnd = True
	                
	    def processMiscEvents(self, event):
	        
	        print "Processing " + event.eventType() + " event"
	        
	        for msg in event:

	            print "MESSAGE: %s" % (msg.tostring())


	def main():
	    
	    sessionOptions = blpapi.SessionOptions()
	    sessionOptions.setServerHost(d_host)
	    sessionOptions.setServerPort(d_port)

	    print "Connecting to %s:%d" % (d_host,d_port)

	    eventHandler = SessionEventHandler()

	    session = blpapi.Session(sessionOptions, eventHandler.processEvent)

	    if not session.startAsync():
	        print "Failed to start session."
	        return
	    
	    global bEnd
	    while bEnd==False:
	        pass
	    
	    session.stop()
	    
	if __name__ == "__main__":
	    print "Bloomberg - EMSX API Example - AssignTrader"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print "Ctrl+C pressed. Stopping..."



Create Order Request
====================


Creating an order requires the user to create a request from the service object of type CreateOrder and fill in the required fields before submitting the request. 

If the handling instruction is for DMA access or any other non-standard handling instructions, EMSX API will not allow users to stage the order from the EMSX API unless the broker enables the broker code for EMSX API.  This is also true for custom Time in Force fields. Any non-standard TIF will also be restricted from staging unless the broker enables the broker code for EMSX API.


This is the python ``code-block`` directive **and** specified


.. code-block:: python

	# CreateOrder.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	CREATE_ORDER            = blpapi.Name("CreateOrder")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	bEnd=False

	class SessionEventHandler():

	    def processEvent(self, event, session):
	        try:
	            if event.eventType() == blpapi.Event.SESSION_STATUS:
	                self.processSessionStatusEvent(event,session)
	            
	            elif event.eventType() == blpapi.Event.SERVICE_STATUS:
	                self.processServiceStatusEvent(event,session)

	            elif event.eventType() == blpapi.Event.RESPONSE:
	                self.processResponseEvent(event)
	            
	            else:
	                self.processMiscEvents(event)
	                
	        except blpapi.Exception as e:
	            print "Exception:  %s" % e.description()
	        return False


	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            else:
	                print msg
	                

	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."

	                service = session.getService(d_service)
	    
	                request = service.createRequest("CreateOrder")

	                # The fields below are mandatory
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_AMOUNT", 1000)
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TIF", "DAY")
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_SIDE", "BUY")

	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                #request.set("EMSX_BASKET_NAME", "HedgingBasket")
	                #request.set("EMSX_BROKER", "BMTB")
	                #request.set("EMSX_CFD_FLAG", "1")
	                #request.set("EMSX_CLEARING_ACCOUNT", "ClrAccName")
	                #request.set("EMSX_CLEARING_FIRM", "FirmName")
	                #request.set("EMSX_CUSTOM_NOTE1", "Note1")
	                #request.set("EMSX_CUSTOM_NOTE2", "Note2")
	                #request.set("EMSX_CUSTOM_NOTE3", "Note3")
	                #request.set("EMSX_CUSTOM_NOTE4", "Note4")
	                #request.set("EMSX_CUSTOM_NOTE5", "Note5")
	                #request.set("EMSX_EXCHANGE_DESTINATION", "ExchDest")
	                #request.set("EMSX_EXEC_INSTRUCTIONS", "AnyInst")
	                #request.set("EMSX_GET_WARNINGS", "0")
	                #request.set("EMSX_GTD_DATE", "20170105")
	                #request.set("EMSX_INVESTOR_ID", "InvID")
	                #request.set("EMSX_LIMIT_PRICE", 123.45)
	                #request.set("EMSX_LOCATE_BROKER", "BMTB")
	                #request.set("EMSX_LOCATE_ID", "SomeID")
	                #request.set("EMSX_LOCATE_REQ", "Y")
	                #request.set("EMSX_NOTES", "Some notes")
	                #request.set("EMSX_ODD_LOT", "0")
	                #request.set("EMSX_ORDER_ORIGIN", "")
	                #request.set("EMSX_ORDER_REF_ID", "UniqueID")
	                #request.set("EMSX_P_A", "P")
	                #request.set("EMSX_RELEASE_TIME", 34341)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_SETTLE_CURRENCY", "USD")
	                #request.set("EMSX_SETTLE_DATE", 20170106)
	                #request.set("EMSX_SETTLE_TYPE", "T+2")
	                #request.set("EMSX_STOP_PRICE", 123.5)

	                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        
                
	    def processResponseEvent(self, event):
	        print "Processing RESPONSE event"
	        
	        for msg in event:
	            
	            print "MESSAGE: %s" % msg.toString()
	            print "CORRELATION ID: %d" % msg.correlationIds()[0].value()


	            if msg.correlationIds()[0].value() == self.requestID.value():
	                print "MESSAGE TYPE: %s" % msg.messageType()
	                
	                if msg.messageType() == ERROR_INFO:
	                    errorCode = msg.getElementAsInteger("ERROR_CODE")
	                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
	                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
	                elif msg.messageType() == CREATE_ORDER:
	                    emsx_sequence = msg.getElementAsInteger("EMSX_SEQUENCE")
	                    message = msg.getElementAsString("MESSAGE")
	                    print "EMSX_SEQUENCE: %d\tMESSAGE: %s" % (emsx_sequence,message)

	                global bEnd
	                bEnd = True
	                
	    def processMiscEvents(self, event):
	        
	        print "Processing " + event.eventType() + " event"
	        
	        for msg in event:

	            print "MESSAGE: %s" % (msg.tostring())


	def main():
	    
	    sessionOptions = blpapi.SessionOptions()
	    sessionOptions.setServerHost(d_host)
	    sessionOptions.setServerPort(d_port)

	    print "Connecting to %s:%d" % (d_host,d_port)

	    eventHandler = SessionEventHandler()

	    session = blpapi.Session(sessionOptions, eventHandler.processEvent)

	    if not session.startAsync():
	        print "Failed to start session."
	        return
	    
	    global bEnd
	    while bEnd==False:
	        pass
	    
	    session.stop()
	    
	if __name__ == "__main__":
	    print "Bloomberg - EMSX API Example - CreateOrder"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print "Ctrl+C pressed. Stopping..."


The mandatory fields for the CreateOrder requests are the following:



Create Order and Route Extended Request
=======================================


Creating an order and routing with strategy requires the user to create a request from the service object of type CreateOrderAndRouteWithStrat and fill in the required fields before submitting the request. 
Mandatory fields for the CreateOrderAndRoute requests are the following. 


.. note:: 

	The user will first need to request *GetBrokers* to get all the brokers the user is enabled for, returned in response. Subsequently the user can then request *GetBrokerStrategies* to get all the broker strategies user is enabled for that particular broker code. 

	Lastly, *GetBrokerStrategyInfo* will get all the fields for the provided broker strategy in the particular order in which they need to be submitted in *CreateOrderAndRouteEx* and *RouteEx* requests.



.. code-block:: python

	# CreateOrderAndRouteEx.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	CREATE_ORDER_AND_ROUTE  = blpapi.Name("CreateOrderAndRouteEx")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	bEnd=False

	class SessionEventHandler():

	    def processEvent(self, event, session):
	        try:
	            if event.eventType() == blpapi.Event.SESSION_STATUS:
	                self.processSessionStatusEvent(event,session)
	            
	            elif event.eventType() == blpapi.Event.SERVICE_STATUS:
	                self.processServiceStatusEvent(event,session)

	            elif event.eventType() == blpapi.Event.RESPONSE:
	                self.processResponseEvent(event)
	            
	            else:
	                self.processMiscEvents(event)
	                
	        except blpapi.Exception as e:
	            print "Exception:  %s" % e.description()
	        return False


	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            else:
	                print msg
	                

	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."

	                service = session.getService(d_service)
	    
	                request = service.createRequest("CreateOrderAndRouteEx")

	                # The fields below are mandatory
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_AMOUNT", 1000)
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TIF", "DAY")
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_SIDE", "BUY")
	                request.set("EMSX_BROKER", "BB")
	                
	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                #request.set("EMSX_BOOKNAME","BookName")
	                #request.set("EMSX_BASKET_NAME", "HedgingBasket")
	                #request.set("EMSX_CFD_FLAG", "1")
	                #request.set("EMSX_CLEARING_ACCOUNT", "ClrAccName")
	                #request.set("EMSX_CLEARING_FIRM", "FirmName")
	                #request.set("EMSX_CUSTOM_NOTE1", "Note1")
	                #request.set("EMSX_CUSTOM_NOTE2", "Note2")
	                #request.set("EMSX_CUSTOM_NOTE3", "Note3")
	                #request.set("EMSX_CUSTOM_NOTE4", "Note4")
	                #request.set("EMSX_CUSTOM_NOTE5", "Note5")
	                #request.set("EMSX_EXCHANGE_DESTINATION", "ExchDest")
	                #request.set("EMSX_EXEC_INSTRUCTIONS", "AnyInst")
	                #request.set("EMSX_GET_WARNINGS", "0")
	                #request.set("EMSX_GTD_DATE", "20170105")
	                #request.set("EMSX_INVESTOR_ID", "InvID")
	                #request.set("EMSX_LIMIT_PRICE", 123.45)
	                #request.set("EMSX_LOCATE_BROKER", "BMTB")
	                #request.set("EMSX_LOCATE_ID", "SomeID")
	                #request.set("EMSX_LOCATE_REQ", "Y")
	                #request.set("EMSX_NOTES", "Some notes")
	                #request.set("EMSX_ODD_LOT", "0")
	                #request.set("EMSX_ORDER_ORIGIN", "")
	                #request.set("EMSX_ORDER_REF_ID", "UniqueID")
	                #request.set("EMSX_P_A", "P")
	                #request.set("EMSX_RELEASE_TIME", 34341)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_ROUTE_REF_ID", "UniqueID")
	                #request.set("EMSX_SETTLE_CURRENCY", "USD")
	                #request.set("EMSX_SETTLE_DATE", 20170106)
	                #request.set("EMSX_SETTLE_TYPE", "T+2")
	                #request.set("EMSX_STOP_PRICE", 123.5)

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                
	    def processResponseEvent(self, event):
	        print "Processing RESPONSE event"
	        
	        for msg in event:
	            
	            print "MESSAGE: %s" % msg.toString()
	            print "CORRELATION ID: %d" % msg.correlationIds()[0].value()


	            if msg.correlationIds()[0].value() == self.requestID.value():
	                print "MESSAGE TYPE: %s" % msg.messageType()
	                
	                if msg.messageType() == ERROR_INFO:
	                    errorCode = msg.getElementAsInteger("ERROR_CODE")
	                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
	                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
	                elif msg.messageType() == CREATE_ORDER_AND_ROUTE:
	                    emsx_sequence = msg.getElementAsInteger("EMSX_SEQUENCE")
	                    emsx_route_id = msg.getElementAsInteger("EMSX_ROUTE_ID")
	                    message = msg.getElementAsString("MESSAGE")
	                    print "EMSX_SEQUENCE: %d\tEMSX_ROUTE_ID: %d\tMESSAGE: %s" % (emsx_sequence,emsx_route_id,message)

	                global bEnd
	                bEnd = True
	                
	    def processMiscEvents(self, event):
	        
	        print "Processing " + event.eventType() + " event"
	        
	        for msg in event:

	            print "MESSAGE: %s" % (msg.tostring())


	def main():
	    
	    sessionOptions = blpapi.SessionOptions()
	    sessionOptions.setServerHost(d_host)
	    sessionOptions.setServerPort(d_port)

	    print "Connecting to %s:%d" % (d_host,d_port)

	    eventHandler = SessionEventHandler()

	    session = blpapi.Session(sessionOptions, eventHandler.processEvent)

	    if not session.startAsync():
	        print "Failed to start session."
	        return
	    
	    global bEnd
	    while bEnd==False:
	        pass
	    
	    session.stop()
	    
	if __name__ == "__main__":
	    print "Bloomberg - EMSX API Example - CreateOrderAndRoute"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print "Ctrl+C pressed. Stopping..."




Create Order And Route Manually Request
=======================================

``CreateOrderAndRouteManually`` request is generally used for phone orders where the placement is external to EMSX API. 

.. code-block:: python

	# CreateOrderAndRouteManually.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	CREATE_ORDER_AND_ROUTE_MANUALLY  = blpapi.Name("CreateOrderAndRouteManually")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	bEnd=False

	class SessionEventHandler():

	    def processEvent(self, event, session):
	        try:
	            if event.eventType() == blpapi.Event.SESSION_STATUS:
	                self.processSessionStatusEvent(event,session)
	            
	            elif event.eventType() == blpapi.Event.SERVICE_STATUS:
	                self.processServiceStatusEvent(event,session)

	            elif event.eventType() == blpapi.Event.RESPONSE:
	                self.processResponseEvent(event)
	            
	            else:
	                self.processMiscEvents(event)
	                
	        except blpapi.Exception as e:
	            print "Exception:  %s" % e.description()
	        return False


	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            else:
	                print msg
	                

	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."

	                service = session.getService(d_service)
	    
	                request = service.createRequest("CreateOrderAndRouteManually")

	                # The fields below are mandatory
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_AMOUNT", 1000)
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TIF", "DAY")
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_SIDE", "BUY")
	                request.set("EMSX_BROKER", "BB")
	            
	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                #request.set("EMSX_CFD_FLAG", "1")
	                #request.set("EMSX_CLEARING_ACCOUNT", "ClrAccName")
	                #request.set("EMSX_CLEARING_FIRM", "FirmName")
	                #request.set("EMSX_EXCHANGE_DESTINATION", "ExchDest")
	                #request.set("EMSX_EXEC_INSTRUCTIONS", "AnyInst")
	                #request.set("EMSX_GET_WARNINGS", "0")
	                #request.set("EMSX_GTD_DATE", "20170105")
	                #request.set("EMSX_INVESTOR_ID", "InvID")
	                #request.set("EMSX_LIMIT_PRICE", 123.45)
	                #request.set("EMSX_LOCATE_BROKER", "BMTB")
	                #request.set("EMSX_LOCATE_ID", "SomeID")
	                #request.set("EMSX_LOCATE_REQ", "Y")
	                #request.set("EMSX_NOTES", "Some notes")
	                #request.set("EMSX_ODD_LOT", "0")
	                #request.set("EMSX_ORDER_ORIGIN", "")
	                #request.set("EMSX_ORDER_REF_ID", "UniqueID")
	                #request.set("EMSX_P_A", "P")
	                #request.set("EMSX_RELEASE_TIME", 34341)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_SETTLE_DATE", 20170106)
	                #request.set("EMSX_STOP_PRICE", 123.5)

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                
	    def processResponseEvent(self, event):
	        print "Processing RESPONSE event"
	        
	        for msg in event:
	            
	            print "MESSAGE: %s" % msg.toString()
	            print "CORRELATION ID: %d" % msg.correlationIds()[0].value()


	            if msg.correlationIds()[0].value() == self.requestID.value():
	                print "MESSAGE TYPE: %s" % msg.messageType()
	                
	                if msg.messageType() == ERROR_INFO:
	                    errorCode = msg.getElementAsInteger("ERROR_CODE")
	                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
	                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
	                elif msg.messageType() == CREATE_ORDER_AND_ROUTE_MANUALLY:
	                    emsx_sequence = msg.getElementAsInteger("EMSX_SEQUENCE")
	                    emsx_route_id = msg.getElementAsInteger("EMSX_ROUTE_ID")
	                    message = msg.getElementAsString("MESSAGE")
	                    print "EMSX_SEQUENCE: %d\tEMSX_ROUTE_ID: %d\tMESSAGE: %s" % (emsx_sequence,emsx_route_id,message)

	                global bEnd
	                bEnd = True
	                
	    def processMiscEvents(self, event):
	        
	        print "Processing " + event.eventType() + " event"
	        
	        for msg in event:

	            print "MESSAGE: %s" % (msg.tostring())


	def main():
	    
	    sessionOptions = blpapi.SessionOptions()
	    sessionOptions.setServerHost(d_host)
	    sessionOptions.setServerPort(d_port)

	    print "Connecting to %s:%d" % (d_host,d_port)

	    eventHandler = SessionEventHandler()

	    session = blpapi.Session(sessionOptions, eventHandler.processEvent)

	    if not session.startAsync():
	        print "Failed to start session."
	        return
	    
	    global bEnd
	    while bEnd==False:
	        pass
	    
	    session.stop()
	    
	if __name__ == "__main__":
	    print "Bloomberg - EMSX API Example - CreateOrderAndRouteManually"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print "Ctrl+C pressed. Stopping..."



Route Extended Request
======================


Creating a route is essentially a buy-side placement to the market. Going forward we will refer all placment as routes in the documentation.


.. code-block:: python

	# RouteEx.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	ROUTE                   = blpapi.Name("Route")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	bEnd=False

	class SessionEventHandler():

	    def processEvent(self, event, session):
	        try:
	            if event.eventType() == blpapi.Event.SESSION_STATUS:
	                self.processSessionStatusEvent(event,session)
	            
	            elif event.eventType() == blpapi.Event.SERVICE_STATUS:
	                self.processServiceStatusEvent(event,session)

	            elif event.eventType() == blpapi.Event.RESPONSE:
	                self.processResponseEvent(event)
	            
	            else:
	                self.processMiscEvents(event)
	                
	        except blpapi.Exception as e:
	            print "Exception:  %s" % e.description()
	        return False


	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            else:
	                print msg
	                

	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."

	                service = session.getService(d_service)
	    
	                request = service.createRequest("RouteEx")

	                # The fields below are mandatory
	                request.set("EMSX_SEQUENCE", 3745217) # Order number
	                request.set("EMSX_AMOUNT", 500)
	                request.set("EMSX_BROKER", "BB")
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_TIF", "DAY")
	            
	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                ##request.set("EMSX_CFD_FLAG", "1")
	                #request.set("EMSX_CLEARING_ACCOUNT", "ClrAccName")
	                #request.set("EMSX_CLEARING_FIRM", "FirmName")
	                #request.set("EMSX_EXEC_INSTRUCTIONS", "AnyInst")
	                #request.set("EMSX_GET_WARNINGS", "0")
	                #request.set("EMSX_GTD_DATE", "20170105")
	                #request.set("EMSX_LIMIT_PRICE", 123.45)
	                #request.set("EMSX_LOCATE_BROKER", "BMTB")
	                #request.set("EMSX_LOCATE_ID", "SomeID")
	                #request.set("EMSX_LOCATE_REQ", "Y")
	                #request.set("EMSX_NOTES", "Some notes")
	                #request.set("EMSX_ODD_LOT", "0")
	                #request.set("EMSX_P_A", "P")
	                #request.set("EMSX_RELEASE_TIME", 34341)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_ROUTE_REF_ID", "UniqueRef")
	                #request.set("EMSX_STOP_PRICE", 123.5)
	                #request.set("EMSX_TRADER_UUID", 1234567)

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                
	    def processResponseEvent(self, event):
	        print "Processing RESPONSE event"
	        
	        for msg in event:
	            
	            print "MESSAGE: %s" % msg.toString()
	            print "CORRELATION ID: %d" % msg.correlationIds()[0].value()


	            if msg.correlationIds()[0].value() == self.requestID.value():
	                print "MESSAGE TYPE: %s" % msg.messageType()
	                
	                if msg.messageType() == ERROR_INFO:
	                    errorCode = msg.getElementAsInteger("ERROR_CODE")
	                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
	                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
	                elif msg.messageType() == ROUTE:
	                    emsx_sequence = msg.getElementAsInteger("EMSX_SEQUENCE")
	                    emsx_route_id = msg.getElementAsInteger("EMSX_ROUTE_ID")
	                    message = msg.getElementAsString("MESSAGE")
	                    print "EMSX_SEQUENCE: %d\tEMSX_ROUTE_ID: %d\tMESSAGE: %s" % (emsx_sequence,emsx_route_id,message)

	                global bEnd
	                bEnd = True
	                
	    def processMiscEvents(self, event):
	        
	        print "Processing " + event.eventType() + " event"
	        
	        for msg in event:

	            print "MESSAGE: %s" % (msg.tostring())


	def main():
	    
	    sessionOptions = blpapi.SessionOptions()
	    sessionOptions.setServerHost(d_host)
	    sessionOptions.setServerPort(d_port)

	    print "Connecting to %s:%d" % (d_host,d_port)

	    eventHandler = SessionEventHandler()

	    session = blpapi.Session(sessionOptions, eventHandler.processEvent)

	    if not session.startAsync():
	        print "Failed to start session."
	        return
	    
	    global bEnd
	    while bEnd==False:
	        pass
	    
	    session.stop()
	    
	if __name__ == "__main__":
	    print "Bloomberg - EMSX API Example - Route"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print "Ctrl+C pressed. Stopping..."




