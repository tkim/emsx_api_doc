#################
EMSX Subscription
#################

EMSX subscription service provides a way of accessing and monitoring real-time updates on orders and routes in the user's blotter outside of ``EMSX<GO>`` function in your Bloomberg terminal.

EMSX subscription sample illustrates how to use both Order and Route subscription service for EMSX API.

Once the subscription is established all the orders and routes in the user's blotter are returned via one or more ``BLP API`` events of type ``SUBSCRIPTION``. Each event is further composed of one or more messages where each message contains all the subscribed fields for a single order or route.

Additionally, any changes to these orders and/or routes will generate events that are passed along as they occur.  These subscriptions can be asynchronous or synchronous but it is best to always approach this with asynchronous event-driven architecture in mind.


.. warning::

    When implementing subscription service, it's important to write the code using two separate .subscribe() events for the order and route subscriptions.


Description of Subscription Messages
====================================


================== =========================================================================
Element Name        Description
================== =========================================================================
``MSG_TYPE``		``MSG_TYPE=E``, this indicates the message is an EMSX API message.	
------------------ -------------------------------------------------------------------------		
``MSG_SUBTYPE``		O = Order & R = Route
------------------ -------------------------------------------------------------------------				
``EVENT_STATUS``    Event status messages (e.g ``INIT_PAINT``, ``NEW_ORDER_ROUTE`` and etc.)
------------------ -------------------------------------------------------------------------
``API_SEQ_NUM``		Unique API sequence number to help detect gaps in the events.		 
------------------ -------------------------------------------------------------------------
``EMSX_SEQUENCE``	Unique order number in EMSX<GO>. 					
------------------ -------------------------------------------------------------------------
``EMSX_ROUTE_ID`` 	Route number, always 0 for order subscription events.			
------------------ -------------------------------------------------------------------------
``EMSX_FILL_ID``	Fill number on routess.
================== =========================================================================


Description of EVENT_STATUS Message
===================================


===================== ===================================================================================
``EVENT_STATUS``   	   Message Type / Description          		  	
===================== ===================================================================================
``EVENT_STATUS = 1``   Heartbeat Message  ``HB_MESSAGE``
--------------------- -----------------------------------------------------------------------------------
``EVENT_STATUS = 4``   Initial Paint Message on all subscription fields ``INIT_PAINT`` 
--------------------- -----------------------------------------------------------------------------------
``EVENT_STATUS = 6``   New Order or Route Message  on all subscription fields ``NEW_ORDER_ROUTE``
--------------------- -----------------------------------------------------------------------------------
``EVENT_STATUS = 7``   This field dynamically updates for existing Order and route ``UPD_ORDER_ROUTE``	
--------------------- -----------------------------------------------------------------------------------
``EVENT_STATUS = 8``   Order and route deletion message, ``DELETION_MESSAGE`` 	
--------------------- -----------------------------------------------------------------------------------
``EVENT_STATUS = 11``  The end of the initial paint message, ``INIT_PAINT_END``
===================== ===================================================================================
	


Full code sample:-

========================== ======================== ========================
`EMSX Subscriptions cpp`_  `EMSX Subscriptions cs`_ `EMSX Subscription vba`_	
-------------------------- ------------------------ ------------------------
`EMSX Subscriptions java`_ `EMSX Subscriptions py`_
========================== ======================== ========================

.. _EMSX Subscriptions cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/EMSXSubscriptions.cpp

.. _EMSX Subscriptions cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/EMSXSubscriptions.cs

.. _EMSX Subscriptions java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/EMSXSubscriptions.java

.. _EMSX Subscriptions py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/EMSXSubscriptions.py

.. _EMSX Subscription vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/EMSXSubscriptions.cls


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


Specify service name and host/port :-


.. code-block:: python

	# EMSXSubscriptions.py

	import blpapi
	import sys


	ORDER_ROUTE_FIELDS              = blpapi.Name("OrderRouteFields")

	SLOW_CONSUMER_WARNING           = blpapi.Name("SlowConsumerWarning")
	SLOW_CONSUMER_WARNING_CLEARED   = blpapi.Name("SlowConsumerWarningCleared")

	SESSION_STARTED                 = blpapi.Name("SessionStarted")
	SESSION_TERMINATED              = blpapi.Name("SessionTerminated")
	SESSION_STARTUP_FAILURE         = blpapi.Name("SessionStartupFailure")
	SESSION_CONNECTION_UP           = blpapi.Name("SessionConnectionUp")
	SESSION_CONNECTION_DOWN         = blpapi.Name("SessionConnectionDown")

	SERVICE_OPENED                  = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE            = blpapi.Name("ServiceOpenFailure")

	SUBSCRIPTION_FAILURE            = blpapi.Name("SubscriptionFailure")
	SUBSCRIPTION_STARTED            = blpapi.Name("SubscriptionStarted")
	SUBSCRIPTION_TERMINATED         = blpapi.Name("SubscriptionTerminated")

	EXCEPTIONS = blpapi.Name("exceptions")
	FIELD_ID = blpapi.Name("fieldId")
	REASON = blpapi.Name("reason")
	CATEGORY = blpapi.Name("category")
	DESCRIPTION = blpapi.Name("description")

	d_service="//blp/emapisvc_beta"
	d_host="localhost"
	d_port=8194
	orderSubscriptionID=blpapi.CorrelationId(98)
	routeSubscriptionID=blpapi.CorrelationId(99)

Process admin events:-

.. code-block:: python

	    def processAdminEvent(self,event):
	        print "Processing ADMIN event"

	        for msg in event:
	            
	            if msg.messageType() == SLOW_CONSUMER_WARNING:
	                print "Warning: Entered Slow Consumer status"
	            elif msg.messageType() ==  SLOW_CONSUMER_WARNING_CLEARED:
	                print "Slow consumer status cleared"

	    def processSessionStatusEvent(self,event,session):
	        print "Processing SESSION_STATUS event"

	        for msg in event:
	            
	            if msg.messageType() == SESSION_STARTED:
	                print "Session started..."
	                session.openServiceAsync(d_service)
	                
	            elif msg.messageType() == SESSION_STARTUP_FAILURE:
	                print >> sys.stderr, "Error: Session startup failed"
	                
	            elif msg.messageType() == SESSION_TERMINATED:
	                print >> sys.stderr, "Error: Session has been terminated"
	                
	            elif msg.messageType() == SESSION_CONNECTION_UP:
	                print "Session connection is up"
	                
	            elif msg.messageType() == SESSION_CONNECTION_DOWN:
	                print >> sys.stderr, "Error: Session connection is down"
	                	                
	    def processServiceStatusEvent(self,event,session):
	        print "Processing SERVICE_STATUS event"
	        
	        for msg in event:
	            
	            if msg.messageType() == SERVICE_OPENED:
	                print "Service opened..."
	                self.createOrderSubscription(session)
	                
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                	                
	    def processSubscriptionStatusEvent(self, event, session):
	        print "Processing SUBSCRIPTION_STATUS event"


Start Subscription:-


.. code-block:: python


	        for msg in event:
	            
	            if msg.messageType() == SUBSCRIPTION_STARTED:
	                
	                print "OrderSubID: %s\tRouteSubID: %s" % (orderSubscriptionID.value(), routeSubscriptionID.value())

	                if msg.correlationIds()[0].value() == orderSubscriptionID.value():
	                    print "Order subscription started successfully"
	                    self.createRouteSubscription(session)
	                    
	                elif msg.correlationIds()[0].value() == routeSubscriptionID.value():
	                    print "Route subscription started successfully"
	                    
	            elif msg.messageType() == SUBSCRIPTION_FAILURE:
	                print >> sys.stderr, "Error: Subscription failed"
	                print >> sys.stderr, "MESSAGE: %s" % (msg)
	                    
	                reason = msg.getElement("reason");
	                errorcode = reason.getElementAsInteger("errorCode")
	                description = reason.getElementAsString("description")
	            
	                print >> sys.stdout, "Error: (%d) %s" % (errorcode, description)                
	                
	            elif msg.messageType() == SUBSCRIPTION_TERMINATED:
	                print >> sys.stderr, "Error: Subscription terminated"
	                print >> sys.stderr, "MESSAGE: %s" % (msg)


Pick and choose the elements and create order subscription:-


.. code-block:: python

  
	    def createOrderSubscription(self, session):
	        
	        print "Create Order subscription"
	        
	        orderTopic = d_service + "/order?fields="
	        orderTopic = orderTopic + "API_SEQ_NUM,"
	        orderTopic = orderTopic + "EMSX_ACCOUNT,"
	        orderTopic = orderTopic + "EMSX_AMOUNT,"
	        orderTopic = orderTopic + "EMSX_ARRIVAL_PRICE,"
	        orderTopic = orderTopic + "EMSX_ASSET_CLASS,"
	        orderTopic = orderTopic + "EMSX_ASSIGNED_TRADER,"
	        orderTopic = orderTopic + "EMSX_AVG_PRICE,"
	        orderTopic = orderTopic + "EMSX_BASKET_NAME,"
	        orderTopic = orderTopic + "EMSX_BASKET_NUM,"
	        orderTopic = orderTopic + "EMSX_BROKER,"
	        orderTopic = orderTopic + "EMSX_BROKER_COMM,"
	        orderTopic = orderTopic + "EMSX_BSE_AVG_PRICE,"
	        orderTopic = orderTopic + "EMSX_BSE_FILLED,"
	        orderTopic = orderTopic + "EMSX_CFD_FLAG,"
	        orderTopic = orderTopic + "EMSX_COMM_DIFF_FLAG,"
	        orderTopic = orderTopic + "EMSX_COMM_RATE,"
	        orderTopic = orderTopic + "EMSX_CURRENCY_PAIR,"
	        orderTopic = orderTopic + "EMSX_DATE,"
	        orderTopic = orderTopic + "EMSX_DAY_AVG_PRICE,"
	        

	        subscriptions = blpapi.SubscriptionList()
	        
	        subscriptions.add(topic=orderTopic,correlationId=orderSubscriptionID)

	        session.subscribe(subscriptions)
	                       

Pick and choose the elements and create route subscription:-


.. code-block:: python


	    def createRouteSubscription(self, session):
	        
	        print "Create Route subscription"
	        
	        routeTopic = d_service + "/route?fields="
	        routeTopic = routeTopic + "API_SEQ_NUM,"
	        routeTopic = routeTopic + "EMSX_AMOUNT,"
	        routeTopic = routeTopic + "EMSX_AVG_PRICE,"
	        routeTopic = routeTopic + "EMSX_BROKER,"
	        routeTopic = routeTopic + "EMSX_BROKER_COMM,"
	        routeTopic = routeTopic + "EMSX_BSE_AVG_PRICE,"
	        routeTopic = routeTopic + "EMSX_BSE_FILLED,"
	        routeTopic = routeTopic + "EMSX_CLEARING_ACCOUNT,"
	        routeTopic = routeTopic + "EMSX_CLEARING_FIRM,"

	        

	        subscriptions = blpapi.SubscriptionList()
	        
	        subscriptions.add(topic=routeTopic,correlationId=routeSubscriptionID)

	        session.subscribe(subscriptions)




