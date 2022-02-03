#################
EMSX Subscription
#################
EMSX subscription service provides a way of accessing and monitoring real-time updates on orders and routes in the user's blotter outside of ``EMSX<GO>`` function in your Bloomberg terminal.

EMSX subscription sample illustrates how to use both Order and Route subscription service for EMSX API.

Once the subscription is established all the orders and routes in the user's blotter are returned via one or more Bloomberg API events of type ``SUBSCRIPTION``. Each event is further composed of one or more messages where each message contains all the subscribed fields for a single order or route.

Additionally, any changes to these orders and/or routes will generate events that are passed along as they occur.  These subscriptions can be asynchronous or synchronous but it is best to always approach this with asynchronous event-driven architecture in mind.


.. warning::

    When implementing subscription service, it's important to write the code using two separate .subscribe() events for the order and route subscriptions.

.. important::

    It's important to unsubscribe before starting the Subscription service. 


Description of Subscription Messages
====================================


================== =========================================================================
Element Name        Description
================== =========================================================================
``MSG_TYPE``		``MSG_TYPE=E``, this indicates the message is an EMSX API message.	
------------------ -------------------------------------------------------------------------		
``MSG_SUB_TYPE``	O = Order & R = Route
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


Description of Event Status Messages
========================================


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
	

Description of Order Status Messages
====================================


===================== ===================================================================================
Order Status    	   Description          		  	
===================== ===================================================================================
``ASSIGN``  		  The route has been cancelled or rejected without fills.							
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CANCEL`` or ``REJECTED``.						
--------------------- -----------------------------------------------------------------------------------
``CANCEL`` 			  The order has been cancelled, no shares filled.                                   
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CANCEL`` or ``REJECTED``.                        
--------------------- -----------------------------------------------------------------------------------
``COMPLETED``		  All Shares have been filled and allocated in OAX for Bloomberg AIM users.
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CANCEL``, ``FILLED``, or ``PARTFILLED``. 
--------------------- -----------------------------------------------------------------------------------
``CXL-PEND``		  The Sell-Side EMSX to EMSX (E2E), order pending cancel acknowledgement. 
--------------------- -----------------------------------------------------------------------------------
``EXPIRED``			  The order is expired.
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CANCEL``, ``FILLED``, or ``PARTFILLED``.
--------------------- -----------------------------------------------------------------------------------
``FILLED`` 			  All shares have been filled, no idle quantity.
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``FILLED``.
--------------------- -----------------------------------------------------------------------------------
``MOD-PEND``		  Only valid for the Sell-Side EMSX to EMSX (E2E) settings.  The order modification 
--------------------- -----------------------------------------------------------------------------------
|                     pending acknowledgement. Fields that can populate: Size, Price, Stop,  GTDDate,
--------------------- -----------------------------------------------------------------------------------
|                     TIF, Type and instruments.  e.g. EMSX_MOD_PEND_STATUS= “Pending Info|Size: 500.0
--------------------- -----------------------------------------------------------------------------------
|                     -> 200.0|Price 2.0000 -> 4.0000|Instr: -> test instr”
--------------------- -----------------------------------------------------------------------------------
``NEW``				  The order has been added/staged; no routes have been created.
--------------------- -----------------------------------------------------------------------------------
``ORD-PEND``		  The Sell-Side EMSX to EMSX (E2E), new order pending acknowledgement. 
--------------------- -----------------------------------------------------------------------------------
``PARTFILLED``		  The order has idle or unfilled shares.
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CANCEL``, ``FILLED``, or ``PARTFILLED``.
--------------------- -----------------------------------------------------------------------------------
``SENT`` 			  The route has been sent to the broker but has not been acknowledged. 
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``SENT``.
--------------------- -----------------------------------------------------------------------------------
``WORKING``			  The route has been sent and acknowledged by the broker or the route has been
--------------------- -----------------------------------------------------------------------------------
|					  partially filled or route has a cancel request pending or rejected. 
--------------------- -----------------------------------------------------------------------------------
|					  Applicable Child Route Status: ``CXLREJ``, ``CXLREQ``, ``CXLRPRQ``, ``CXLRPRJ``,  
|                     ``HOLD``, ``PARTFILLED``, or ``WORKING``.   										
===================== ===================================================================================

	
Description of the Child Route Status Messages
==============================================


===================== ======================================================================================
Route Status    	   Description          		  	
===================== ======================================================================================
``A-SENT``			  The route has been sent for allocation for Bloomberg STP users.
--------------------- --------------------------------------------------------------------------------------
``ALLOCATED`` 		  The route has been allocated for Bloomberg STP users.
--------------------- --------------------------------------------------------------------------------------
``BUST``			  The route fill has been busted by the execution broker.
--------------------- --------------------------------------------------------------------------------------
``CANCEL``  		  The route has been canceled.
--------------------- --------------------------------------------------------------------------------------
``CORRECTED`` 		  The route fill has been corrected by the execution broker.
--------------------- --------------------------------------------------------------------------------------
``CXLREJ`` 			  The cancel request is rejected by the execution broker. 
--------------------- --------------------------------------------------------------------------------------
``CXLREP``			  The cancel replace request is accepted by the execution broker.
--------------------- --------------------------------------------------------------------------------------
``CXLREQ`` 			  The cancel request is sent and is pending with the execution broker.
--------------------- --------------------------------------------------------------------------------------
``CXLRPRJ`` 		  The cancel replace request is rejected by the execution broker.
--------------------- --------------------------------------------------------------------------------------
``CXLRPRQ``			  The cancel replace request is sent and is pending with the execution broker.
--------------------- --------------------------------------------------------------------------------------
``DONE`` 			  The route has been marked done for the day by the execution broker. 
--------------------- --------------------------------------------------------------------------------------
``FILLED`` 			  The route has been completely filled.
--------------------- --------------------------------------------------------------------------------------
``HOLD`` 			  The shared are committed to a dark pool. 
--------------------- --------------------------------------------------------------------------------------
``OA-SENT`` 		  The route has been sent for allocation in OAX for Bloomberg AIM users
--------------------- --------------------------------------------------------------------------------------
``OMS PEND`` 		  The route has been sent to buy-side OMS for compliance check, pending acknowledgement.
--------------------- --------------------------------------------------------------------------------------
``PARTFILLED`` 		  The route has been partilly filled. 
--------------------- --------------------------------------------------------------------------------------
``QUEUED`` 			  The route is created but not released until the defined time in release time.
--------------------- --------------------------------------------------------------------------------------
``REJECTED`` 		  The route has been rejected by the execution broker.
--------------------- --------------------------------------------------------------------------------------
``REPPEN`` 			  The route replace request is pending with the execution broker.
--------------------- --------------------------------------------------------------------------------------
``ROUTE-ERR`` 		  The route has an error, please check with EMSX trade desk and/or executing broker. 
--------------------- --------------------------------------------------------------------------------------
``SENT`` 			  The route has been sent to the broker but have not been acknowledged by the broker.
--------------------- --------------------------------------------------------------------------------------
``WORKING`` 		  The route has been sent and acknowledged by the executing broker.
===================== ======================================================================================


Description of the Child Route Status Changes
=============================================


+----------------------+------------+------------+-------------------------------------------------+
|Field                 |Previous    |New Value   |Definition                                       |
|                      |Value       |            |                                                 |     
+======================+============+============+=================================================+
|``EMSX_STATUS``       |null        |``SENT``    |New route (placement) created.                   |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``SENT``    |``SENT``    |Field update on sent.                            |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``SENT``    |``WORKING`` |ACK received from the broker.                    |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``PARTFILL``|First fill or multiple fills. (<100%)            |
+----------------------+------------+------------+                                                 |
|``EMSX_WORKING``      |n           |<n and >0   |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``PARTFILL``|``PARTFILL``|Middle fill or multiple fills. (<100%)           |
+----------------------+------------+------------+                                                 |
|``EMSX_WORKING``      |n           |<n and >0   |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``PARTFILL``|``FILLED``  |Final fill or multiple fills. (100%)             |
+----------------------+------------+------------+                                                 |
|``EMSX_WORKING``      |>0          |0           |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``FILLED``  |Full single fill.                                |
+----------------------+------------+------------+                                                 |
|``EMSX_WORKING``      |>0          |0           |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``FILLED``  |Historic 100% fill on ``INIT_PAINT``.            |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``WORKING`` |Working route (placement) on ``INIT_PAINT``.     |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``PARTFILL``|Part filled route (placement) on ``INIT_PAINT``. |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``CXLREQ``  |Cancel requested on route in ``INIT_PAINT``.     |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``CXLREQ``  |Cancel route request sent.                       |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLREQ``  |``WORKING`` |Broker rejected cancel request.                  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLREQ``  |``CXLPEN``  |Broker sent ACK for cancel request.              |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLPEN``  |``WORKING`` |Broker rejected cancel request.                  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLREQ``  |``CANCEL``  |Broker cancelled route from request.             |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLPEN``  |``CANCEL``  |Broker cancelled route from request.             |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``PARTFILL``|``CXLREQ``  |Cancel requested on part filled route.           |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLREQ``  |``PARTFILL``|Broker rejected cancel request.                  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLPEN``  |``PARTFILL``|Broker rejected cancel request.                  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``CXLRPRQ`` |Modify (cancel/replace) request sent to broker.  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLRPRQ`` |``REPPEN``  |Broker sent ACK for modify request.              |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``REPPEN``  |``WORKING`` |Broker rejected modify request on working route. |
+----------------------+------------+------------+                                                 |
|``EMSX_BROKER_STATUS``|n/a         |``CXLRPRJ`` |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``REPPEN``  |``WORKING`` |Broker accepted and applied the modify request   |
+----------------------+------------+------------+on working route. (placement)                    |
|``EMSX_BROKER_STATUS``|n/a         |``MODIFIED``|                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``PARTFILL``|``CXLRPRQ`` |Modify (cancel/replace) request sent to broker.  |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``REPPEN``  |``PARTFILL``|Broker rejected modify request on part filled    |
+----------------------+------------+------------+route. (placement)                               |
|``EMSX_BROKER_STATUS``|n/a         |``CXLRPRJ`` |                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``REPPEN``  |``PARTFILL``|Broker accepted and applied the modify request   |
+----------------------+------------+------------+on part filled route. (placement)                |
|``EMSX_BROKER_STATUS``|n/a         |``MODIFIED``|                                                 |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``SENT``    |``REJECTED``|Broker rejected the order from sent status.      |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``REJECTED``|``INIT_PAINT`` shows route (placement) rejected. |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |null        |``CANCEL``  |``INIT_PAINT`` shows route (placement) cancelled.|
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``CXLRPRQ`` |``WORKING`` |Modify rejected from request.                    |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``PARTFILL``|``CANCEL``  |Part filled route cancelled by broker.           |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``CANCEL``  |Working route cancelled by broker.               |
+----------------------+------------+------------+-------------------------------------------------+
|``EMSX_STATUS``       |``WORKING`` |``REJECTED``|Route rejected from working.                     |
+----------------------+------------+------------+-------------------------------------------------+

Description of Fills using Route Subscription
=============================================
The real-time fills in EMSX API are delivered through the route subscription service.  However, to capture the full state of the order, we always recommend the client listens to both the order and route subscription service.

The following elements provide the route updates that can be calculated to obtain the real-time incoming fills for a live route.


+-----------------------------+------------------------------------------------------------------------+
|Field                        |Definition                                                              |
+=============================+========================================================================+
|``EMSX_LAST_FILL_DATE``      |  | ``INT32`` ``ROUTE`` The date of the last fill based on the user's   |
|                             |  | time zone. This field is applicable to trades on a route level, and |
|                             |  | does not populate on a per security basis.                          | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_MARKET``         |  | ``STRING`` ``ROUTE`` The last market of execution for a trade as    |
|                             |  | returned by the broker.This field is applicable to trades on a route|
|                             |  | level, and does not populate on a per security basis.               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_PRICE``          |  | ``FLOAT64`` ``ROUTE`` The last execution price for a trade. This    |
|                             |  | field is applicable to trades on a route level, and does not        |
|                             |  | populate ona per security basis.                                    |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_SHARES``         |  | ``INT32`` ``ROUTE`` The last executed quantity for a trade. This    |
|                             |  | field is applicable to trades on a route level, and does not        |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_FILL_TIME``      |  | ``INT32`` ``ROUTE`` The time of the last fill based on seconds from |
|                             |  | midnight in the user's time zone. This field is applicable to trades|
|                             |  | on a route level, and does not populate on a per security basis.    | 
+-----------------------------+------------------------------------------------------------------------+


The ``EMSX_FILL_ID`` is the transaction sequence number to keep track of the individual fills. One thing to keep in mind is that this is a reflection of the fills and thus you will typically see the ``EMSX_FILL_ID`` to show 0, 2, 3, 4,.. 8,9,.. 14, and etc.  In most cases, the ``EMSX_FILL_ID`` = 1 is not reflected as this is an ``ACK`` message from the broker. The ``EMSX_FILL_ID`` is a unique ID per fill in sequential order but does not necessarily tie to the actual Fill numbers and will skip fill events that are not directly tied to a fill. 

+-----------------------------+------------------------------------------------------------------------+
|Field                        |Definition                                                              |
+=============================+========================================================================+
|``EMSX_FILL_ID``             |  | ``INT32`` ``STATIC`` ``O,R`` The fill number associated with a      |
|                             |  | route. This field is applicable to trades on an order and/or route  |
|                             |  | level, and does not populate on a per security basis.               | 
+-----------------------------+------------------------------------------------------------------------+

The ``EMSX_ROUTE_LAST_UPDATE_TIME`` is timestamp based on the number of seconds from midnight that reflects the last update of a route. This can be fill or any other route-based update events.

+-------------------------------+----------------------------------------------------------------------+
|Field                          |Definition                                                            |
+===============================+======================================================================+
|``EMSX_ROUTE_LAST_UPDATE_TIME``|  | ``INT32`` ``ROUTE`` The time stamp of the last execution or       |
|                               |  | cancellation on a route. This field is applicable to trades on a  |
|                               |  | route level and does not populate on a per security basis.        |
+-------------------------------+----------------------------------------------------------------------+



Description of Order Expiration Logic
=====================================
The parent orders in EMSX follow an expiration logic that first puts orders into view only mode before it gets removed from 
EMSX blotter.


.. note::

	TIF = Time in force
	
	h = hours

	GT covers both GTC and GTD.


====== ==== =========== ==================================================================
Asset  TIF   Event      Description
------ ---- ----------- ------------------------------------------------------------------
Equity Day  ``EXPIRED`` Exchange close + 8h
------ ---- ----------- ------------------------------------------------------------------
Equity Day  ``DELETED`` Exchange close + 8h  + 16h
------ ---- ----------- ------------------------------------------------------------------
Equity GT   ``EXPIRED`` On GTD date it's same as day order if there are no open routes
------ ---- ----------- ------------------------------------------------------------------
Equity GT   ``EXPIRED`` On GTD date if open routes, then redated to current GTD date + 24h	
------ ---- ----------- ------------------------------------------------------------------
Future Day  ``EXPIRED`` Earlier of Exchange close + 4h  or start of the next session
------ ---- ----------- ------------------------------------------------------------------
Future Day  ``DELETED`` Earlier of Exchange close + 4h or start of the next session + 20h
------ ---- ----------- ------------------------------------------------------------------
Future GT   ``EXPIRED`` On GTD date it's same as day order if there are no open routes
------ ---- ----------- ------------------------------------------------------------------
Future GT   ``EXPIRED`` On GTD date if open routes, then redated to current GTD date + 24h
------ ---- ----------- ------------------------------------------------------------------
Option Day  ``EXPIRED`` Exchange close + 4h
------ ---- ----------- ------------------------------------------------------------------
Option Day  ``DELETED`` Exchange close + 4h + 20h
------ ---- ----------- ------------------------------------------------------------------
Option GT   ``EXPIRED`` On GTD date it's same as day order if there are no open routes.
------ ---- ----------- ------------------------------------------------------------------
Option GT   ``EXPIRED`` On GTD date if open routes, then redated to current GTD date + 24h
====== ==== =========== ==================================================================


Description of Route Expiration Logic
=====================================
All equities routes in EMSX will expire 8 hours after the exchange midnight. All futures and options routes in EMSX will 
expire 24 hours after exchange close time.


Full code sample:-

========================== ======================== =========================
`EMSX Subscriptions cpp`_  `EMSX Subscriptions cs`_ `EMSX Subscription vba`_	
-------------------------- ------------------------ -------------------------
`EMSX Subscriptions java`_ `EMSX Subscriptions py`_ `EMSX Subscriptions py2`_ 
========================== ======================== =========================

.. _EMSX Subscriptions cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/EMSXSubscriptions.cpp

.. _EMSX Subscriptions cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/EMSXSubscriptions.cs

.. _EMSX Subscriptions java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/EMSXSubscriptions.java

.. _EMSX Subscriptions py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/EMSXSubscriptions.py

.. _EMSX Subscriptions py2: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/py2_EMSXSubscriptions.py

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


Output:-

.. code-block:: none

	C:\Users\_scripts>py -3 EMSXSubscriptions_beta.py
	Bloomberg - EMSX API Example - EMSXSubscriptions
	Connecting to localhost:8194
	Press ENTER to quit
	Processing SESSION_STATUS event
	Session connection is up
	Processing SESSION_STATUS event
	Session started...
	Processing SERVICE_STATUS event
	Service opened...
	Create Order subscription
	Processing SUBSCRIPTION_STATUS event
	Order subscription started successfully
	Create Route subscription

	ORDER MESSAGE: CorrelationID(98)   Status(4)
	MESSAGE: OrderRouteFields = {
		MSG_TYPE = "E"
		MSG_SUB_TYPE = "O"
		EMSX_SEQUENCE = 4747927
		EMSX_ROUTE_ID = 0
		EMSX_FILL_ID = 0
		API_SEQ_NUM = 1
		EVENT_STATUS = 4
		EMSX_ACCOUNT = ""
		EMSX_AMOUNT = 6000
		EMSX_ASSET_CLASS = "Equity"
		EMSX_ASSIGNED_TRADER = ""
		EMSX_AVG_PRICE = 161.330000
		EMSX_BASKET_NAME = ""
		EMSX_BASKET_NUM = 0
		EMSX_BLOCK_ID = ""
		EMSX_BROKER = ""
		EMSX_BROKER_COMM = 0.000000
		EMSX_BSE_AVG_PRICE = 0.000000
		EMSX_BSE_FILLED = 0
		EMSX_BUYSIDE_LEI = ""
		EMSX_CFD_FLAG = "N"
		EMSX_CLIENT_IDENTIFICATION = ""
		EMSX_COMM_RATE = 0.000000
		EMSX_CURRENCY_PAIR = ""
		EMSX_DATE = 20200113
		EMSX_DAY_AVG_PRICE = 161.330000
		EMSX_DAY_FILL = 360
		EMSX_DIR_BROKER_FLAG = "N"
		EMSX_EXCHANGE = "US"
		EMSX_EXCHANGE_DESTINATION = "ANY"
		EMSX_EXEC_INSTRUCTION = ""
		EMSX_FILLED = 360
		EMSX_GPI = ""
		EMSX_GTD_DATE = 0
		EMSX_HAND_INSTRUCTION = "ANY"
		EMSX_IDLE_AMOUNT = 5580
		EMSX_INVESTOR_ID = "InvID"
		EMSX_ISIN = "US5949181045"
		EMSX_LIMIT_PRICE = 0.000000
		EMSX_MIFID_II_INSTRUCTION = ""
		EMSX_NOTES = ""
		EMSX_NSE_AVG_PRICE = 0.000000
		EMSX_NSE_FILLED = 0
		EMSX_ORD_REF_ID = ""
		EMSX_ORDER_AS_OF_DATE = 20200113
		EMSX_ORDER_AS_OF_TIME_MICROSEC = 49794.000000
		EMSX_ORDER_TYPE = "MKT"
		EMSX_PERCENT_REMAIN = 94.000000
		EMSX_PM_UUID = 6767714
		EMSX_PORT_MGR = "TKIM94"
		EMSX_PORT_NAME = ""
		EMSX_PORT_NUM = 9999
		EMSX_POSITION = ""
		EMSX_PRINCIPAL = 58078.800000
		EMSX_PRODUCT = "Equity"
		EMSX_QUEUED_DATE = 0
		EMSX_QUEUED_TIME = 0
		EMSX_QUEUED_TIME_MICROSEC = 0.000000
		EMSX_REASON_CODE = ""
		EMSX_REASON_DESC = ""
		EMSX_REMAIN_BALANCE = 5640.000000
		EMSX_ROUTE_PRICE = 0.000000
		EMSX_SEC_NAME = "MICROSOFT CORP"
		EMSX_SEDOL = "2588173 "
		EMSX_SETTLE_AMOUNT = 0.000000
		EMSX_SETTLE_DATE = 0
		EMSX_SI = "N"
		EMSX_SIDE = "BUY"
		EMSX_START_AMOUNT = 1100
		EMSX_STATUS = "WORKING"
		EMSX_STEP_OUT_BROKER = ""
		EMSX_STOP_PRICE = 0.000000
		EMSX_STRATEGY_END_TIME = 0
		EMSX_STRATEGY_PART_RATE1 = 0.000000
		EMSX_STRATEGY_PART_RATE2 = 0.000000
		EMSX_STRATEGY_STYLE = ""
		EMSX_STRATEGY_TYPE = ""
		EMSX_TICKER = "MSFT US Equity"
		EMSX_TIF = "DAY"
		EMSX_TIME_STAMP = 49794
		EMSX_TIME_STAMP_MICROSEC = 49794.341000
		EMSX_TRAD_UUID = 6767714
		EMSX_TRADE_DESK = ""
		EMSX_TRADER = "TKIM94"
		EMSX_TRADER_NOTES = ""
		EMSX_TS_ORDNUM = -4747927
		EMSX_TYPE = "MKT"
		EMSX_UNDERLYING_TICKER = "Loading"
		EMSX_USER_COMM_AMOUNT = 0.000000
		EMSX_USER_COMM_RATE = 0.000000
		EMSX_USER_FEES = 0.000000
		EMSX_USER_NET_MONEY = 58078.800000
		EMSX_WORK_PRICE = 0.000000
		EMSX_WORKING = 60
		EMSX_YELLOW_KEY = "Equity"
		EMSX_STRATEGY_START_TIME = 0
		EMSX_CUSTOM_NOTE1 = ""
		EMSX_CUSTOM_NOTE2 = ""
		EMSX_CUSTOM_NOTE3 = ""
		EMSX_CUSTOM_NOTE4 = ""
		EMSX_CUSTOM_NOTE5 = ""
		EMSX_MOD_PEND_STATUS = ""
	}

	API_SEQ_NUM: 1
	EMSX_ACCOUNT:
	EMSX_AMOUNT: 6000
	EMSX_ASSET_CLASS: Equity
	EMSX_ASSIGNED_TRADER:
	EMSX_AVG_PRICE: 161
	EMSX_BASKET_NAME:
	EMSX_BASKET_NUM: 0
	EMSX_BLOCK_ID:
	EMSX_BROKER:
	EMSX_BROKER_COMM: 0
	EMSX_BSE_AVG_PRICE: 0
	EMSX_BSE_FILLED: 0
	EMSX_BUYSIDE_LEI:
	EMSX_CFD_FLAG: N
	EMSX_CLIENT_IDENTIFICATION:
	EMSX_COMM_DIFF_FLAG:
	EMSX_COMM_RATE: 0
	EMSX_CUSTOM_NOTE1:
	EMSX_CUSTOM_NOTE2:
	EMSX_CUSTOM_NOTE3:
	EMSX_CUSTOM_NOTE4:
	EMSX_CUSTOM_NOTE5:
	EMSX_CURRENCY_PAIR:
	EMSX_DATE: 20200113
	EMSX_DAY_AVG_PRICE: 161
	EMSX_DAY_FILL: 360
	EMSX_DIR_BROKER_FLAG: N
	EMSX_EXCHANGE: US
	EMSX_EXCHANGE_DESTINATION: ANY
	EMSX_EXEC_INSTRUCTION:
	EMSX_FILL_ID: 0
	EMSX_FILLED: 360
	EMSX_GPI:
	EMSX_GTD_DATE: 0
	EMSX_HAND_INSTRUCTION: ANY
	EMSX_IDLE_AMOUNT: 5580
	EMSX_INVESTOR_ID: InvID
	EMSX_ISIN: US5949181045
	EMSX_LIMIT_PRICE: 0.00000000
	EMSX_MIFID_II_INSTRUCTION:
	EMSX_MOD_PEND_STATUS:
	EMSX_NOTES:
	EMSX_NSE_AVG_PRICE: 0
	EMSX_NSE_FILLED: 0
	EMSX_ORD_REF_ID:
	EMSX_ORDER_AS_OF_DATE: 20200113
	EMSX_ORDER_AS_OF_TIME_MICROSEC: 49794.00000000
	EMSX_ORDER_TYPE: MKT
	EMSX_ORIGINATE_TRADER:
	EMSX_ORIGINATE_TRADER_FIRM:
	EMSX_PERCENT_REMAIN: 94
	EMSX_PM_UUID: 6767714
	EMSX_PORT_MGR: TKIM94
	EMSX_PORT_NAME:
	EMSX_PORT_NUM: 9999
	EMSX_POSITION:
	EMSX_PRINCIPAL: 58078
	EMSX_PRODUCT: Equity
	EMSX_QUEUED_DATE: 0
	EMSX_QUEUED_TIME: 0
	EMSX_QUEUED_TIME_MICROSEC: 0.00000000
	EMSX_REASON_CODE:
	EMSX_REASON_DESC:
	EMSX_REMAIN_BALANCE: 5640
	EMSX_ROUTE_ID: 0
	EMSX_ROUTE_PRICE: 0
	EMSX_SEC_NAME: MICROSOFT CORP
	EMSX_SEDOL: 2588173
	EMSX_SEQUENCE: 4747927
	EMSX_SETTLE_AMOUNT: 0
	EMSX_SETTLE_DATE: 0
	EMSX_SI: N
	EMSX_SIDE: BUY
	EMSX_START_AMOUNT: 1100
	EMSX_STATUS: WORKING
	EMSX_STEP_OUT_BROKER:
	EMSX_STOP_PRICE: 0
	EMSX_STRATEGY_END_TIME: 0
	EMSX_STRATEGY_PART_RATE1: 0
	EMSX_STRATEGY_PART_RATE2: 0
	EMSX_STRATEGY_STYLE:
	EMSX_STRATEGY_TYPE:
	EMSX_TICKER: MSFT US Equity
	EMSX_TIF: DAY
	EMSX_TIME_STAMP: 49794
	EMSX_TIME_STAMP_MICROSEC: 49794.34100000
	EMSX_TRAD_UUID: 6767714
	EMSX_TRADE_DESK:
	EMSX_TRADER: TKIM94
	EMSX_TRADER_NOTES:
	EMSX_TS_ORDNUM: -4747927
	EMSX_TYPE: MKT
	EMSX_UNDERLYING_TICKER: Loading
	EMSX_USER_COMM_AMOUNT: 0
	EMSX_USER_COMM_RATE: 0
	EMSX_USER_FEES: 0
	EMSX_USER_NET_MONEY: 58078
	EMSX_WORK_PRICE: 0
	EMSX_WORKING: 60
	EMSX_YELLOW_KEY: Equity
	Processing SUBSCRIPTION_STATUS event
	Route subscription started successfully

	ORDER MESSAGE: CorrelationID(98)   Status(4)
	MESSAGE: OrderRouteFields = {
		MSG_TYPE = "E"
		MSG_SUB_TYPE = "O"
		EMSX_SEQUENCE = 4747928
		EMSX_ROUTE_ID = 0
		EMSX_FILL_ID = 0
		API_SEQ_NUM = 2
		EVENT_STATUS = 4
		EMSX_ACCOUNT = ""
		EMSX_AMOUNT = 1100
		EMSX_ASSET_CLASS = "Equity"
		EMSX_ASSIGNED_TRADER = ""
		EMSX_AVG_PRICE = 161.330000
		EMSX_BASKET_NAME = ""
		EMSX_BASKET_NUM = 0
		EMSX_BLOCK_ID = ""
		EMSX_BROKER = ""
		EMSX_BROKER_COMM = 0.000000
		EMSX_BSE_AVG_PRICE = 0.000000
		EMSX_BSE_FILLED = 0
		EMSX_BUYSIDE_LEI = ""
		EMSX_CFD_FLAG = "N"
		EMSX_CLIENT_IDENTIFICATION = ""
		EMSX_COMM_RATE = 0.000000
		EMSX_CURRENCY_PAIR = ""
		EMSX_DATE = 20200113
		EMSX_DAY_AVG_PRICE = 161.330000
		EMSX_DAY_FILL = 198
		EMSX_DIR_BROKER_FLAG = "N"
		EMSX_EXCHANGE = "US"
		EMSX_EXCHANGE_DESTINATION = "ANY"
		EMSX_EXEC_INSTRUCTION = ""
		EMSX_FILLED = 198
		EMSX_GPI = ""
		EMSX_GTD_DATE = 0
		EMSX_HAND_INSTRUCTION = "ANY"
		EMSX_IDLE_AMOUNT = 600
		EMSX_INVESTOR_ID = ""
		EMSX_ISIN = "US5949181045"
		EMSX_LIMIT_PRICE = 0.000000
		EMSX_MIFID_II_INSTRUCTION = ""
		EMSX_NOTES = ""
		EMSX_NSE_AVG_PRICE = 0.000000
		EMSX_NSE_FILLED = 0
		EMSX_ORD_REF_ID = ""
		EMSX_ORDER_AS_OF_DATE = 20200113
		EMSX_ORDER_AS_OF_TIME_MICROSEC = 49797.000000
		EMSX_ORDER_TYPE = "MKT"
		EMSX_PERCENT_REMAIN = 82.000000
		EMSX_PM_UUID = 6767714
		EMSX_PORT_MGR = "TKIM94"
		EMSX_PORT_NAME = ""
		EMSX_PORT_NUM = 9999
		EMSX_POSITION = ""
		EMSX_PRINCIPAL = 31943.340000
		EMSX_PRODUCT = "Equity"
		EMSX_QUEUED_DATE = 0
		EMSX_QUEUED_TIME = 0
		EMSX_QUEUED_TIME_MICROSEC = 0.000000
		EMSX_REASON_CODE = ""
		EMSX_REASON_DESC = ""
		EMSX_REMAIN_BALANCE = 902.000000
		EMSX_ROUTE_PRICE = 0.000000
		EMSX_SEC_NAME = "MICROSOFT CORP"
		EMSX_SEDOL = "2588173 "
		EMSX_SETTLE_AMOUNT = 0.000000
		EMSX_SETTLE_DATE = 0
		EMSX_SI = "N"
		EMSX_SIDE = "BUY"
		EMSX_START_AMOUNT = 1100
		EMSX_STATUS = "WORKING"
		EMSX_STEP_OUT_BROKER = ""
		EMSX_STOP_PRICE = 0.000000
		EMSX_STRATEGY_END_TIME = 0
		EMSX_STRATEGY_PART_RATE1 = 0.000000
		EMSX_STRATEGY_PART_RATE2 = 0.000000
		EMSX_STRATEGY_STYLE = ""
		EMSX_STRATEGY_TYPE = ""
		EMSX_TICKER = "MSFT US Equity"
		EMSX_TIF = "DAY"
		EMSX_TIME_STAMP = 49797
		EMSX_TIME_STAMP_MICROSEC = 49797.410000
		EMSX_TRAD_UUID = 6767714
		EMSX_TRADE_DESK = ""
		EMSX_TRADER = "TKIM94"
		EMSX_TRADER_NOTES = ""
		EMSX_TS_ORDNUM = -4747928
		EMSX_TYPE = "MKT"
		EMSX_UNDERLYING_TICKER = "Loading"
		EMSX_USER_COMM_AMOUNT = 0.000000
		EMSX_USER_COMM_RATE = 0.000000
		EMSX_USER_FEES = 0.000000
		EMSX_USER_NET_MONEY = 31943.340000
		EMSX_WORK_PRICE = 0.000000
		EMSX_WORKING = 302
		EMSX_YELLOW_KEY = "Equity"
		EMSX_STRATEGY_START_TIME = 0
		EMSX_CUSTOM_NOTE1 = ""
		EMSX_CUSTOM_NOTE2 = ""
		EMSX_CUSTOM_NOTE3 = ""
		EMSX_CUSTOM_NOTE4 = ""
		EMSX_CUSTOM_NOTE5 = ""
		EMSX_MOD_PEND_STATUS = ""
	}

	API_SEQ_NUM: 2
	EMSX_ACCOUNT:
	EMSX_AMOUNT: 1100
	EMSX_ASSET_CLASS: Equity
	EMSX_ASSIGNED_TRADER:
	EMSX_AVG_PRICE: 161
	EMSX_BASKET_NAME:
	EMSX_BASKET_NUM: 0
	EMSX_BLOCK_ID:
	EMSX_BROKER:
	EMSX_BROKER_COMM: 0
	EMSX_BSE_AVG_PRICE: 0
	EMSX_BSE_FILLED: 0
	EMSX_BUYSIDE_LEI:
	EMSX_CFD_FLAG: N
	EMSX_CLIENT_IDENTIFICATION:
	EMSX_COMM_DIFF_FLAG:
	EMSX_COMM_RATE: 0
	EMSX_CUSTOM_NOTE1:
	EMSX_CUSTOM_NOTE2:
	EMSX_CUSTOM_NOTE3:
	EMSX_CUSTOM_NOTE4:
	EMSX_CUSTOM_NOTE5:
	EMSX_CURRENCY_PAIR:
	EMSX_DATE: 20200113
	EMSX_DAY_AVG_PRICE: 161
	EMSX_DAY_FILL: 198
	EMSX_DIR_BROKER_FLAG: N
	EMSX_EXCHANGE: US
	EMSX_EXCHANGE_DESTINATION: ANY
	EMSX_EXEC_INSTRUCTION:
	EMSX_FILL_ID: 0
	EMSX_FILLED: 198
	EMSX_GPI:
	EMSX_GTD_DATE: 0
	EMSX_HAND_INSTRUCTION: ANY
	EMSX_IDLE_AMOUNT: 600
	EMSX_INVESTOR_ID:
	EMSX_ISIN: US5949181045
	EMSX_LIMIT_PRICE: 0.00000000
	EMSX_MIFID_II_INSTRUCTION:
	EMSX_MOD_PEND_STATUS:
	EMSX_NOTES:
	EMSX_NSE_AVG_PRICE: 0
	EMSX_NSE_FILLED: 0
	EMSX_ORD_REF_ID:
	EMSX_ORDER_AS_OF_DATE: 20200113
	EMSX_ORDER_AS_OF_TIME_MICROSEC: 49797.00000000
	EMSX_ORDER_TYPE: MKT
	EMSX_ORIGINATE_TRADER:
	EMSX_ORIGINATE_TRADER_FIRM:
	EMSX_PERCENT_REMAIN: 82
	EMSX_PM_UUID: 6767714
	EMSX_PORT_MGR: TKIM94
	EMSX_PORT_NAME:
	EMSX_PORT_NUM: 9999
	EMSX_POSITION:
	EMSX_PRINCIPAL: 31943
	EMSX_PRODUCT: Equity
	EMSX_QUEUED_DATE: 0
	EMSX_QUEUED_TIME: 0
	EMSX_QUEUED_TIME_MICROSEC: 0.00000000
	EMSX_REASON_CODE:
	EMSX_REASON_DESC:
	EMSX_REMAIN_BALANCE: 902
	EMSX_ROUTE_ID: 0
	EMSX_ROUTE_PRICE: 0
	EMSX_SEC_NAME: MICROSOFT CORP
	EMSX_SEDOL: 2588173
	EMSX_SEQUENCE: 4747928
	EMSX_SETTLE_AMOUNT: 0
	EMSX_SETTLE_DATE: 0
	EMSX_SI: N
	EMSX_SIDE: BUY
	EMSX_START_AMOUNT: 1100
	EMSX_STATUS: WORKING
	EMSX_STEP_OUT_BROKER:
	EMSX_STOP_PRICE: 0
	EMSX_STRATEGY_END_TIME: 0
	EMSX_STRATEGY_PART_RATE1: 0
	EMSX_STRATEGY_PART_RATE2: 0
	EMSX_STRATEGY_STYLE:
	EMSX_STRATEGY_TYPE:
	EMSX_TICKER: MSFT US Equity
	EMSX_TIF: DAY
	EMSX_TIME_STAMP: 49797
	EMSX_TIME_STAMP_MICROSEC: 49797.41000000
	EMSX_TRAD_UUID: 6767714
	EMSX_TRADE_DESK:
	EMSX_TRADER: TKIM94
	EMSX_TRADER_NOTES:
	EMSX_TS_ORDNUM: -4747928
	EMSX_TYPE: MKT
	EMSX_UNDERLYING_TICKER: Loading
	EMSX_USER_COMM_AMOUNT: 0
	EMSX_USER_COMM_RATE: 0
	EMSX_USER_FEES: 0
	EMSX_USER_NET_MONEY: 31943
	EMSX_WORK_PRICE: 0
	EMSX_WORKING: 302
	EMSX_YELLOW_KEY: Equity
	Order - End of initial paint

	ROUTE MESSAGE: CorrelationID(99)   Status(4)
	MESSAGE: OrderRouteFields = {
		MSG_TYPE = "E"
		MSG_SUB_TYPE = "R"
		EMSX_SEQUENCE = 4747928
		EMSX_ROUTE_ID = 1
		EMSX_FILL_ID = 13
		API_SEQ_NUM = 1
		EVENT_STATUS = 4
		EMSX_AMOUNT = 500
		EMSX_AVG_PRICE = 161.330000
		EMSX_BROKER = "BB"
		EMSX_BROKER_COMM = 0.000000
		EMSX_BSE_AVG_PRICE = 0.000000
		EMSX_BSE_FILLED = 0
		EMSX_BUYSIDE_LEI = ""
		EMSX_CLIENT_IDENTIFICATION = ""
		EMSX_COMM_RATE = 0.000000
		EMSX_CURRENCY_PAIR = ""
		EMSX_DAY_AVG_PRICE = 161.330000
		EMSX_DAY_FILL = 198
		EMSX_EXCHANGE_DESTINATION = "ANY"
		EMSX_EXEC_INSTRUCTION = ""
		EMSX_FILLED = 198
		EMSX_GPI = ""
		EMSX_GTD_DATE = 0
		EMSX_HAND_INSTRUCTION = "ANY"
		EMSX_LIMIT_PRICE = 0.000000
		EMSX_MIFID_II_INSTRUCTION = ""
		EMSX_NOTES = ""
		EMSX_NSE_AVG_PRICE = 0.000000
		EMSX_NSE_FILLED = 0
		EMSX_ORDER_TYPE = "MKT"
		EMSX_PERCENT_REMAIN = 60.400000
		EMSX_PRINCIPAL = 31943.340000
		EMSX_QUEUED_DATE = 0
		EMSX_QUEUED_TIME = 0
		EMSX_QUEUED_TIME_MICROSEC = 0.000000
		EMSX_REASON_CODE = ""
		EMSX_REASON_DESC = ""
		EMSX_REMAIN_BALANCE = 302.000000
		EMSX_ROUTE_PRICE = 162.835000
		EMSX_SETTLE_AMOUNT = 0.000000
		EMSX_SETTLE_DATE = 20200115
		EMSX_STATUS = "REPPEN"
		EMSX_STOP_PRICE = 0.000000
		EMSX_STRATEGY_END_TIME = 0
		EMSX_STRATEGY_PART_RATE1 = 0.000000
		EMSX_STRATEGY_PART_RATE2 = 0.000000
		EMSX_STRATEGY_STYLE = ""
		EMSX_STRATEGY_TYPE = ""
		EMSX_TIF = "DAY"
		EMSX_TIME_STAMP = 49904
		EMSX_TIME_STAMP_MICROSEC = 49904.123000
		EMSX_TYPE = "MKT"
		EMSX_USER_COMM_AMOUNT = 0.000000
		EMSX_USER_COMM_RATE = 0.000000
		EMSX_USER_FEES = 0.000000
		EMSX_USER_NET_MONEY = 31943.340000
		EMSX_WORKING = 302
		EMSX_APA_MIC = ""
		EMSX_BROKER_LEI = ""
		EMSX_BROKER_SI = ""
		EMSX_BROKER_STATUS = ""
		EMSX_CLEARING_ACCOUNT = ""
		EMSX_CLEARING_FIRM = ""
		EMSX_CUSTOM_ACCOUNT = ""
		EMSX_EXECUTE_BROKER = ""
		EMSX_IS_MANUAL_ROUTE = 0
		EMSX_LAST_CAPACITY = ""
		EMSX_LAST_FILL_DATE = 20200113
		EMSX_LAST_FILL_TIME = 50074
		EMSX_LAST_FILL_TIME_MICROSEC = 50074.215000
		EMSX_LAST_MARKET = ""
		EMSX_LAST_PRICE = 0.000000
		EMSX_LAST_SHARES = 0
		EMSX_LEG_FILL_DATE_ADDED = 0
		EMSX_LEG_FILL_PRICE = 0.000000
		EMSX_LEG_FILL_SEQ_NO = 0
		EMSX_LEG_FILL_SHARES = 0.000000
		EMSX_LEG_FILL_SIDE = ""
		EMSX_LEG_FILL_TICKER = ""
		EMSX_MISC_FEES = 0.000000
		EMSX_ML_ID = ""
		EMSX_ML_LEG_QUANTITY = 500
		EMSX_ML_NUM_LEGS = 0
		EMSX_ML_PERCENT_FILLED = 39.600000
		EMSX_ML_RATIO = 0.000000
		EMSX_ML_REMAIN_BALANCE = -198.000000
		EMSX_ML_STRATEGY = ""
		EMSX_ML_TOTAL_QUANTITY = 0
		EMSX_OTC_FLAG = ""
		EMSX_P_A = ""
		EMSX_ROUTE_AS_OF_DATE = 20200113
		EMSX_ROUTE_AS_OF_TIME_MICROSEC = 49904.123000
		EMSX_ROUTE_CREATE_DATE = 20200113
		EMSX_ROUTE_CREATE_TIME = 49904
		EMSX_ROUTE_CREATE_TIME_MICROSEC = 49904.123000
		EMSX_ROUTE_LAST_UPDATE_TIME = 50083
		EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC = 50083.276000
		EMSX_ROUTE_REF_ID = "MyRouteRef2"
		EMSX_STRATEGY_START_TIME = 0
		EMSX_TRADE_REPORTING_INDICATOR = ""
		EMSX_TRANSACTION_REPORTING_MIC = ""
		EMSX_URGENCY_LEVEL = 0
		EMSX_WAIVER_FLAG = ""
	}

	API_SEQ_NUM: 1
	EMSX_AMOUNT: 500
	EMSX_APA_MIC:
	EMSX_AVG_PRICE: 161
	EMSX_BROKER: BB
	EMSX_BROKER_COMM: 0
	EMSX_BROKER_LEI:
	EMSX_BROKER_SI:
	EMSX_BROKER_STATUS:
	EMSX_BSE_AVG_PRICE: 0
	EMSX_BSE_FILLED: 0
	EMSX_BUYSIDE_LEI:
	EMSX_CLEARING_ACCOUNT:
	EMSX_CLEARING_FIRM:
	EMSX_CLIENT_IDENTIFICATION:
	EMSX_COMM_DIFF_FLAG:
	EMSX_COMM_RATE: 0
	EMSX_CURRENCY_PAIR:
	EMSX_CUSTOM_ACCOUNT:
	EMSX_DAY_AVG_PRICE: 161
	EMSX_DAY_FILL: 198
	EMSX_EXCHANGE_DESTINATION: ANY
	EMSX_EXEC_INSTRUCTION:
	EMSX_EXECUTE_BROKER:
	EMSX_FILL_ID: 13
	EMSX_FILLED: 198
	EMSX_GPI:
	EMSX_GTD_DATE: 0
	EMSX_HAND_INSTRUCTION: ANY
	EMSX_IS_MANUAL_ROUTE: 0
	EMSX_LAST_CAPACITY:
	EMSX_LAST_FILL_DATE: 20200113
	EMSX_LAST_FILL_TIME: 50074
	EMSX_LAST_FILL_TIME_MICROSEC: 50074.21500000
	EMSX_LAST_MARKET:
	EMSX_LAST_PRICE: 0
	EMSX_LAST_SHARES: 0
	EMSX_LEG_FILL_DATE_ADDED: 0
	EMSX_LEG_FILL_PRICE: 0.00000000
	EMSX_LEG_FILL_SEQ_NO: 0
	EMSX_LEG_FILL_SHARES: 0.00000000
	EMSX_LEG_FILL_SIDE:
	EMSX_LEG_FILL_TICKER:
	EMSX_LEG_FILL_TIME_ADDED: 0
	EMSX_LIMIT_PRICE: 0.00000000
	EMSX_MIFID_II_INSTRUCTION:
	EMSX_MISC_FEES: 0
	EMSX_ML_ID:
	EMSX_ML_LEG_QUANTITY: 500
	EMSX_ML_NUM_LEGS: 0
	EMSX_ML_PERCENT_FILLED: 39
	EMSX_ML_RATIO: 0
	EMSX_ML_REMAIN_BALANCE: -198
	EMSX_ML_STRATEGY:
	EMSX_ML_TOTAL_QUANTITY: 0
	EMSX_NOTES:
	EMSX_NSE_AVG_PRICE: 0
	EMSX_NSE_FILLED: 0
	EMSX_ORDER_TYPE: MKT
	EMSX_OTC_FLAG:
	EMSX_P_A:
	EMSX_PERCENT_REMAIN: 60
	EMSX_PRINCIPAL: 31943
	EMSX_QUEUED_DATE: 0
	EMSX_QUEUED_TIME: 0
	EMSX_QUEUED_TIME_MICROSEC: 0.00000000
	EMSX_REASON_CODE:
	EMSX_REASON_DESC:
	EMSX_REMAIN_BALANCE: 302
	EMSX_ROUTE_AS_OF_DATE: 20200113
	EMSX_ROUTE_AS_OF_TIME_MICROSEC: 49904.12300000
	EMSX_ROUTE_CREATE_DATE: 20200113
	EMSX_ROUTE_CREATE_TIME: 49904
	EMSX_ROUTE_CREATE_TIME_MICROSEC: 49904.12300000
	EMSX_ROUTE_ID: 1
	EMSX_ROUTE_LAST_UPDATE_TIME: 50083
	EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC: 50083.27600000
	EMSX_ROUTE_PRICE: 162
	EMSX_ROUTE_REF_ID: MyRouteRef2
	EMSX_SEQUENCE: 4747928
	EMSX_SETTLE_AMOUNT: 0
	EMSX_SETTLE_DATE: 20200115
	EMSX_STATUS: REPPEN
	EMSX_STOP_PRICE: 0
	EMSX_STRATEGY_END_TIME: 0
	EMSX_STRATEGY_PART_RATE1: 0
	EMSX_STRATEGY_PART_RATE2: 0
	EMSX_STRATEGY_START_TIME: 0
	EMSX_STRATEGY_STYLE:
	EMSX_STRATEGY_TYPE:
	EMSX_TIF: DAY
	EMSX_TIME_STAMP: 49904
	EMSX_TIME_STAMP_MICROSEC: 49904.12300000
	EMSX_TRADE_REPORTING_INDICATOR:
	EMSX_TRANSACTION_REPORTING_MIC:
	EMSX_TYPE: MKT
	EMSX_URGENCY_LEVEL: 0
	EMSX_USER_COMM_AMOUNT: 0
	EMSX_USER_COMM_RATE: 0
	EMSX_USER_FEES: 0
	EMSX_USER_NET_MONEY: 31943
	EMSX_WAIVER_FLAG:
	EMSX_WORKING: 302
	EMSX_ROUTE_AS_OF_DATE: 20200113

	ROUTE MESSAGE: CorrelationID(99)   Status(4)
	MESSAGE: OrderRouteFields = {
		MSG_TYPE = "E"
		MSG_SUB_TYPE = "R"
		EMSX_SEQUENCE = 4747927
		EMSX_ROUTE_ID = 2
		EMSX_FILL_ID = 9
		API_SEQ_NUM = 2
		EVENT_STATUS = 4
		EMSX_AMOUNT = 200
		EMSX_AVG_PRICE = 161.330000
		EMSX_BROKER = "BB"
		EMSX_BROKER_COMM = 0.000000
		EMSX_BSE_AVG_PRICE = 0.000000
		EMSX_BSE_FILLED = 0
		EMSX_BUYSIDE_LEI = ""
		EMSX_CLIENT_IDENTIFICATION = ""
		EMSX_COMM_RATE = 0.000000
		EMSX_CURRENCY_PAIR = ""
		EMSX_DAY_AVG_PRICE = 161.330000
		EMSX_DAY_FILL = 140
		EMSX_EXCHANGE_DESTINATION = "ANY"
		EMSX_EXEC_INSTRUCTION = "Work"
		EMSX_FILLED = 140
		EMSX_GPI = ""
		EMSX_GTD_DATE = 0
		EMSX_HAND_INSTRUCTION = "ANY"
		EMSX_LIMIT_PRICE = 0.000000
		EMSX_MIFID_II_INSTRUCTION = ""
		EMSX_NOTES = "Some notes"
		EMSX_NSE_AVG_PRICE = 0.000000
		EMSX_NSE_FILLED = 0
		EMSX_ORDER_TYPE = "MKT"
		EMSX_PERCENT_REMAIN = 30.000000
		EMSX_PRINCIPAL = 22586.200000
		EMSX_QUEUED_DATE = 0
		EMSX_QUEUED_TIME = 0
		EMSX_QUEUED_TIME_MICROSEC = 0.000000
		EMSX_REASON_CODE = ""
		EMSX_REASON_DESC = ""
		EMSX_REMAIN_BALANCE = 60.000000
		EMSX_ROUTE_PRICE = 162.785000
		EMSX_SETTLE_AMOUNT = 0.000000
		EMSX_SETTLE_DATE = 20200115
		EMSX_STATUS = "PARTFILL"
		EMSX_STOP_PRICE = 0.000000
		EMSX_STRATEGY_END_TIME = 0
		EMSX_STRATEGY_PART_RATE1 = 0.000000
		EMSX_STRATEGY_PART_RATE2 = 0.000000
		EMSX_STRATEGY_STYLE = ""
		EMSX_STRATEGY_TYPE = ""
		EMSX_TIF = "DAY"
		EMSX_TIME_STAMP = 50313
		EMSX_TIME_STAMP_MICROSEC = 50313.841000
		EMSX_TYPE = "MKT"
		EMSX_USER_COMM_AMOUNT = 0.000000
		EMSX_USER_COMM_RATE = 0.000000
		EMSX_USER_FEES = 0.000000
		EMSX_USER_NET_MONEY = 22586.200000
		EMSX_WORKING = 60
		EMSX_APA_MIC = ""
		EMSX_BROKER_LEI = ""
		EMSX_BROKER_SI = ""
		EMSX_BROKER_STATUS = ""
		EMSX_CLEARING_ACCOUNT = ""
		EMSX_CLEARING_FIRM = ""
		EMSX_CUSTOM_ACCOUNT = ""
		EMSX_EXECUTE_BROKER = ""
		EMSX_IS_MANUAL_ROUTE = 0
		EMSX_LAST_CAPACITY = ""
		EMSX_LAST_FILL_DATE = 20200113
		EMSX_LAST_FILL_TIME = 50443
		EMSX_LAST_FILL_TIME_MICROSEC = 50443.877000
		EMSX_LAST_MARKET = "N"
		EMSX_LAST_PRICE = 161.330000
		EMSX_LAST_SHARES = 20
		EMSX_LEG_FILL_DATE_ADDED = 0
		EMSX_LEG_FILL_PRICE = 0.000000
		EMSX_LEG_FILL_SEQ_NO = 0
		EMSX_LEG_FILL_SHARES = 0.000000
		EMSX_LEG_FILL_SIDE = ""
		EMSX_LEG_FILL_TICKER = ""
		EMSX_MISC_FEES = 0.000000
		EMSX_ML_ID = ""
		EMSX_ML_LEG_QUANTITY = 200
		EMSX_ML_NUM_LEGS = 0
		EMSX_ML_PERCENT_FILLED = 70.000000
		EMSX_ML_RATIO = 0.000000
		EMSX_ML_REMAIN_BALANCE = -140.000000
		EMSX_ML_STRATEGY = ""
		EMSX_ML_TOTAL_QUANTITY = 0
		EMSX_OTC_FLAG = ""
		EMSX_P_A = ""
		EMSX_ROUTE_AS_OF_DATE = 20200113
		EMSX_ROUTE_AS_OF_TIME_MICROSEC = 50313.841000
		EMSX_ROUTE_CREATE_DATE = 20200113
		EMSX_ROUTE_CREATE_TIME = 50313
		EMSX_ROUTE_CREATE_TIME_MICROSEC = 50313.841000
		EMSX_ROUTE_LAST_UPDATE_TIME = 50443
		EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC = 50443.877000
		EMSX_ROUTE_REF_ID = ""
		EMSX_STRATEGY_START_TIME = 0
		EMSX_TRADE_REPORTING_INDICATOR = ""
		EMSX_TRANSACTION_REPORTING_MIC = ""
		EMSX_URGENCY_LEVEL = 0
		EMSX_WAIVER_FLAG = ""
	}

	API_SEQ_NUM: 2
	EMSX_AMOUNT: 200
	EMSX_APA_MIC:
	EMSX_AVG_PRICE: 161
	EMSX_BROKER: BB
	EMSX_BROKER_COMM: 0
	EMSX_BROKER_LEI:
	EMSX_BROKER_SI:
	EMSX_BROKER_STATUS:
	EMSX_BSE_AVG_PRICE: 0
	EMSX_BSE_FILLED: 0
	EMSX_BUYSIDE_LEI:
	EMSX_CLEARING_ACCOUNT:
	EMSX_CLEARING_FIRM:
	EMSX_CLIENT_IDENTIFICATION:
	EMSX_COMM_DIFF_FLAG:
	EMSX_COMM_RATE: 0
	EMSX_CURRENCY_PAIR:
	EMSX_CUSTOM_ACCOUNT:
	EMSX_DAY_AVG_PRICE: 161
	EMSX_DAY_FILL: 140
	EMSX_EXCHANGE_DESTINATION: ANY
	EMSX_EXEC_INSTRUCTION: Work
	EMSX_EXECUTE_BROKER:
	EMSX_FILL_ID: 9
	EMSX_FILLED: 140
	EMSX_GPI:
	EMSX_GTD_DATE: 0
	EMSX_HAND_INSTRUCTION: ANY
	EMSX_IS_MANUAL_ROUTE: 0
	EMSX_LAST_CAPACITY:
	EMSX_LAST_FILL_DATE: 20200113
	EMSX_LAST_FILL_TIME: 50443
	EMSX_LAST_FILL_TIME_MICROSEC: 50443.87700000
	EMSX_LAST_MARKET: N
	EMSX_LAST_PRICE: 161
	EMSX_LAST_SHARES: 20
	EMSX_LEG_FILL_DATE_ADDED: 0
	EMSX_LEG_FILL_PRICE: 0.00000000
	EMSX_LEG_FILL_SEQ_NO: 0
	EMSX_LEG_FILL_SHARES: 0.00000000
	EMSX_LEG_FILL_SIDE:
	EMSX_LEG_FILL_TICKER:
	EMSX_LEG_FILL_TIME_ADDED: 0
	EMSX_LIMIT_PRICE: 0.00000000
	EMSX_MIFID_II_INSTRUCTION:
	EMSX_MISC_FEES: 0
	EMSX_ML_ID:
	EMSX_ML_LEG_QUANTITY: 200
	EMSX_ML_NUM_LEGS: 0
	EMSX_ML_PERCENT_FILLED: 70
	EMSX_ML_RATIO: 0
	EMSX_ML_REMAIN_BALANCE: -140
	EMSX_ML_STRATEGY:
	EMSX_ML_TOTAL_QUANTITY: 0
	EMSX_NOTES: Some notes
	EMSX_NSE_AVG_PRICE: 0
	EMSX_NSE_FILLED: 0
	EMSX_ORDER_TYPE: MKT
	EMSX_OTC_FLAG:
	EMSX_P_A:
	EMSX_PERCENT_REMAIN: 30
	EMSX_PRINCIPAL: 22586
	EMSX_QUEUED_DATE: 0
	EMSX_QUEUED_TIME: 0
	EMSX_QUEUED_TIME_MICROSEC: 0.00000000
	EMSX_REASON_CODE:
	EMSX_REASON_DESC:
	EMSX_REMAIN_BALANCE: 60
	EMSX_ROUTE_AS_OF_DATE: 20200113
	EMSX_ROUTE_AS_OF_TIME_MICROSEC: 50313.84100000
	EMSX_ROUTE_CREATE_DATE: 20200113
	EMSX_ROUTE_CREATE_TIME: 50313
	EMSX_ROUTE_CREATE_TIME_MICROSEC: 50313.84100000
	EMSX_ROUTE_ID: 2
	EMSX_ROUTE_LAST_UPDATE_TIME: 50443
	EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC: 50443.87700000
	EMSX_ROUTE_PRICE: 162
	EMSX_ROUTE_REF_ID:
	EMSX_SEQUENCE: 4747927
	EMSX_SETTLE_AMOUNT: 0
	EMSX_SETTLE_DATE: 20200115
	EMSX_STATUS: PARTFILL
	EMSX_STOP_PRICE: 0
	EMSX_STRATEGY_END_TIME: 0
	EMSX_STRATEGY_PART_RATE1: 0
	EMSX_STRATEGY_PART_RATE2: 0
	EMSX_STRATEGY_START_TIME: 0
	EMSX_STRATEGY_STYLE:
	EMSX_STRATEGY_TYPE:
	EMSX_TIF: DAY
	EMSX_TIME_STAMP: 50313
	EMSX_TIME_STAMP_MICROSEC: 50313.84100000
	EMSX_TRADE_REPORTING_INDICATOR:
	EMSX_TRANSACTION_REPORTING_MIC:
	EMSX_TYPE: MKT
	EMSX_URGENCY_LEVEL: 0
	EMSX_USER_COMM_AMOUNT: 0
	EMSX_USER_COMM_RATE: 0
	EMSX_USER_FEES: 0
	EMSX_USER_NET_MONEY: 22586
	EMSX_WAIVER_FLAG:
	EMSX_WORKING: 60
	EMSX_ROUTE_AS_OF_DATE: 20200113

	ROUTE MESSAGE: CorrelationID(99)   Status(4)
	MESSAGE: OrderRouteFields = {
		MSG_TYPE = "E"
		MSG_SUB_TYPE = "R"
		EMSX_SEQUENCE = 4747927
		EMSX_ROUTE_ID = 1
		EMSX_FILL_ID = 12
		API_SEQ_NUM = 3
		EVENT_STATUS = 4
		EMSX_AMOUNT = 220
		EMSX_AVG_PRICE = 161.330000
		EMSX_BROKER = "BB"
		EMSX_BROKER_COMM = 0.000000
		EMSX_BSE_AVG_PRICE = 0.000000
		EMSX_BSE_FILLED = 0
		EMSX_BUYSIDE_LEI = ""
		EMSX_CLIENT_IDENTIFICATION = ""
		EMSX_COMM_RATE = 0.000000
		EMSX_CURRENCY_PAIR = ""
		EMSX_DAY_AVG_PRICE = 161.330000
		EMSX_DAY_FILL = 220
		EMSX_EXCHANGE_DESTINATION = "ANY"
		EMSX_EXEC_INSTRUCTION = ""
		EMSX_FILLED = 220
		EMSX_GPI = ""
		EMSX_GTD_DATE = 0
		EMSX_HAND_INSTRUCTION = "ANY"
		EMSX_LIMIT_PRICE = 0.000000
		EMSX_MIFID_II_INSTRUCTION = ""
		EMSX_NOTES = ""
		EMSX_NSE_AVG_PRICE = 0.000000
		EMSX_NSE_FILLED = 0
		EMSX_ORDER_TYPE = "MKT"
		EMSX_PERCENT_REMAIN = 0.000000
		EMSX_PRINCIPAL = 35492.600000
		EMSX_QUEUED_DATE = 0
		EMSX_QUEUED_TIME = 0
		EMSX_QUEUED_TIME_MICROSEC = 0.000000
		EMSX_REASON_CODE = ""
		EMSX_REASON_DESC = ""
		EMSX_REMAIN_BALANCE = 0.000000
		EMSX_ROUTE_PRICE = 162.835000
		EMSX_SETTLE_AMOUNT = 0.000000
		EMSX_SETTLE_DATE = 20200115
		EMSX_STATUS = "FILLED"
		EMSX_STOP_PRICE = 0.000000
		EMSX_STRATEGY_END_TIME = 0
		EMSX_STRATEGY_PART_RATE1 = 0.000000
		EMSX_STRATEGY_PART_RATE2 = 0.000000
		EMSX_STRATEGY_STYLE = ""
		EMSX_STRATEGY_TYPE = ""
		EMSX_TIF = "DAY"
		EMSX_TIME_STAMP = 49904
		EMSX_TIME_STAMP_MICROSEC = 49904.074000
		EMSX_TYPE = "MKT"
		EMSX_USER_COMM_AMOUNT = 0.000000
		EMSX_USER_COMM_RATE = 0.000000
		EMSX_USER_FEES = 0.000000
		EMSX_USER_NET_MONEY = 35492.600000
		EMSX_WORKING = 0
		EMSX_APA_MIC = ""
		EMSX_BROKER_LEI = ""
		EMSX_BROKER_SI = ""
		EMSX_BROKER_STATUS = ""
		EMSX_CLEARING_ACCOUNT = ""
		EMSX_CLEARING_FIRM = ""
		EMSX_CUSTOM_ACCOUNT = ""
		EMSX_EXECUTE_BROKER = ""
		EMSX_IS_MANUAL_ROUTE = 0
		EMSX_LAST_CAPACITY = ""
		EMSX_LAST_FILL_DATE = 20200113
		EMSX_LAST_FILL_TIME = 50104
		EMSX_LAST_FILL_TIME_MICROSEC = 50104.210000
		EMSX_LAST_MARKET = "N"
		EMSX_LAST_PRICE = 161.330000
		EMSX_LAST_SHARES = 22
		EMSX_LEG_FILL_DATE_ADDED = 0
		EMSX_LEG_FILL_PRICE = 0.000000
		EMSX_LEG_FILL_SEQ_NO = 0
		EMSX_LEG_FILL_SHARES = 0.000000
		EMSX_LEG_FILL_SIDE = ""
		EMSX_LEG_FILL_TICKER = ""
		EMSX_MISC_FEES = 0.000000
		EMSX_ML_ID = ""
		EMSX_ML_LEG_QUANTITY = 220
		EMSX_ML_NUM_LEGS = 0
		EMSX_ML_PERCENT_FILLED = 100.000000
		EMSX_ML_RATIO = 0.000000
		EMSX_ML_REMAIN_BALANCE = -220.000000
		EMSX_ML_STRATEGY = ""
		EMSX_ML_TOTAL_QUANTITY = 0
		EMSX_OTC_FLAG = ""
		EMSX_P_A = ""
		EMSX_ROUTE_AS_OF_DATE = 20200113
		EMSX_ROUTE_AS_OF_TIME_MICROSEC = 49904.074000
		EMSX_ROUTE_CREATE_DATE = 20200113
		EMSX_ROUTE_CREATE_TIME = 49904
		EMSX_ROUTE_CREATE_TIME_MICROSEC = 49904.074000
		EMSX_ROUTE_LAST_UPDATE_TIME = 50104
		EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC = 50104.210000
		EMSX_ROUTE_REF_ID = "MyRouteRef1"
		EMSX_STRATEGY_START_TIME = 0
		EMSX_TRADE_REPORTING_INDICATOR = ""
		EMSX_TRANSACTION_REPORTING_MIC = ""
		EMSX_URGENCY_LEVEL = 0
		EMSX_WAIVER_FLAG = ""
	}

	API_SEQ_NUM: 3
	EMSX_AMOUNT: 220
	EMSX_APA_MIC:
	EMSX_AVG_PRICE: 161
	EMSX_BROKER: BB
	EMSX_BROKER_COMM: 0
	EMSX_BROKER_LEI:
	EMSX_BROKER_SI:
	EMSX_BROKER_STATUS:
	EMSX_BSE_AVG_PRICE: 0
	EMSX_BSE_FILLED: 0
	EMSX_BUYSIDE_LEI:
	EMSX_CLEARING_ACCOUNT:
	EMSX_CLEARING_FIRM:
	EMSX_CLIENT_IDENTIFICATION:
	EMSX_COMM_DIFF_FLAG:
	EMSX_COMM_RATE: 0
	EMSX_CURRENCY_PAIR:
	EMSX_CUSTOM_ACCOUNT:
	EMSX_DAY_AVG_PRICE: 161
	EMSX_DAY_FILL: 220
	EMSX_EXCHANGE_DESTINATION: ANY
	EMSX_EXEC_INSTRUCTION:
	EMSX_EXECUTE_BROKER:
	EMSX_FILL_ID: 12
	EMSX_FILLED: 220
	EMSX_GPI:
	EMSX_GTD_DATE: 0
	EMSX_HAND_INSTRUCTION: ANY
	EMSX_IS_MANUAL_ROUTE: 0
	EMSX_LAST_CAPACITY:
	EMSX_LAST_FILL_DATE: 20200113
	EMSX_LAST_FILL_TIME: 50104
	EMSX_LAST_FILL_TIME_MICROSEC: 50104.21000000
	EMSX_LAST_MARKET: N
	EMSX_LAST_PRICE: 161
	EMSX_LAST_SHARES: 22
	EMSX_LEG_FILL_DATE_ADDED: 0
	EMSX_LEG_FILL_PRICE: 0.00000000
	EMSX_LEG_FILL_SEQ_NO: 0
	EMSX_LEG_FILL_SHARES: 0.00000000
	EMSX_LEG_FILL_SIDE:
	EMSX_LEG_FILL_TICKER:
	EMSX_LEG_FILL_TIME_ADDED: 0
	EMSX_LIMIT_PRICE: 0.00000000
	EMSX_MIFID_II_INSTRUCTION:
	EMSX_MISC_FEES: 0
	EMSX_ML_ID:
	EMSX_ML_LEG_QUANTITY: 220
	EMSX_ML_NUM_LEGS: 0
	EMSX_ML_PERCENT_FILLED: 100
	EMSX_ML_RATIO: 0
	EMSX_ML_REMAIN_BALANCE: -220
	EMSX_ML_STRATEGY:
	EMSX_ML_TOTAL_QUANTITY: 0
	EMSX_NOTES:
	EMSX_NSE_AVG_PRICE: 0
	EMSX_NSE_FILLED: 0
	EMSX_ORDER_TYPE: MKT
	EMSX_OTC_FLAG:
	EMSX_P_A:
	EMSX_PERCENT_REMAIN: 0
	EMSX_PRINCIPAL: 35492
	EMSX_QUEUED_DATE: 0
	EMSX_QUEUED_TIME: 0
	EMSX_QUEUED_TIME_MICROSEC: 0.00000000
	EMSX_REASON_CODE:
	EMSX_REASON_DESC:
	EMSX_REMAIN_BALANCE: 0
	EMSX_ROUTE_AS_OF_DATE: 20200113
	EMSX_ROUTE_AS_OF_TIME_MICROSEC: 49904.07400000
	EMSX_ROUTE_CREATE_DATE: 20200113
	EMSX_ROUTE_CREATE_TIME: 49904
	EMSX_ROUTE_CREATE_TIME_MICROSEC: 49904.07400000
	EMSX_ROUTE_ID: 1
	EMSX_ROUTE_LAST_UPDATE_TIME: 50104
	EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC: 50104.21000000
	EMSX_ROUTE_PRICE: 162
	EMSX_ROUTE_REF_ID: MyRouteRef1
	EMSX_SEQUENCE: 4747927
	EMSX_SETTLE_AMOUNT: 0
	EMSX_SETTLE_DATE: 20200115
	EMSX_STATUS: FILLED
	EMSX_STOP_PRICE: 0
	EMSX_STRATEGY_END_TIME: 0
	EMSX_STRATEGY_PART_RATE1: 0
	EMSX_STRATEGY_PART_RATE2: 0
	EMSX_STRATEGY_START_TIME: 0
	EMSX_STRATEGY_STYLE:
	EMSX_STRATEGY_TYPE:
	EMSX_TIF: DAY
	EMSX_TIME_STAMP: 49904
	EMSX_TIME_STAMP_MICROSEC: 49904.07400000
	EMSX_TRADE_REPORTING_INDICATOR:
	EMSX_TRANSACTION_REPORTING_MIC:
	EMSX_TYPE: MKT
	EMSX_URGENCY_LEVEL: 0
	EMSX_USER_COMM_AMOUNT: 0
	EMSX_USER_COMM_RATE: 0
	EMSX_USER_FEES: 0
	EMSX_USER_NET_MONEY: 35492
	EMSX_WAIVER_FLAG:
	EMSX_WORKING: 0
	EMSX_ROUTE_AS_OF_DATE: 20200113
	Route - End of initial paint
	O.
	R.
	O.
	R.
	Processing SESSION_STATUS event
	Error: Session connection is down
	Processing SESSION_STATUS event
	Error: Session has been terminated
	Ctrl+C pressed. Stopping...


