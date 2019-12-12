#################################
Buy-Side Request/Response Service
#################################
The EMSX API allows developers to use the Request/Response services for order and route creation, modification, 
queries related to orders and routes as well as EMSX Team details. Depending on the type of action required, the 
application programmer must create a specific request, populate it with required parameters and send that request to 
the EMSX API service, which provides the response. Communication with the request/response service requires the 
following steps:

	#. Create a session (if session does not yet exist).

	#. Connect session to ``//blp/emapisvc_beta`` or ``//blp/emapisvc`` service and start it.
	
	#. Fetch a service object from the session representing emapisvc.
	
	#.  Use the service object from above to create a Request object of the desired type
	
	#. Send request object via sendRequest method of session object, pass object of type EventQueue to the sendRequest.
	
	#. Loop through the EventQueue object until event of type ``Event::RESPONSE`` is read.

These are initialized in the constructor as below and are then available for the life of the application for submission of various requests. 


Assign Trader Request
=====================
The ``AssignTrader`` request allows EMSX API to reassign order to another user UUID. A typical setup will have the different UUID as another part of the TEAM setup for the order creater UUID. This will allow systematically generated trades to be reassigned to another human trader if need be from the EMSX API.

Assigned trader must be in same ``EMBR<GO>`` group for this to work. ``EMBR<GO>`` is an internal Bloomberg function the account managers will use to set this feature on behalf of the client. The EMSX account manager will check off the ability to reassign before the ``AssignTrader`` request will work. Once this feature is on, trading on behalf other UUID feature will no longer work for that team.


Full code sample:-

====================== =================== ====================
`Assign Trader cpp`_   `Assign Trader cs`_ `Assign Trader vba`_	
---------------------- ------------------- --------------------
`Assign Trader java`_  `Assign Trader py`_
====================== =================== ====================

.. _Assign Trader cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/AssignTrader.cpp

.. _Assign Trader cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/AssignTrader.cs

.. _Assign Trader java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/AssignTrader.java

.. _Assign Trader py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/AssignTrader.py

.. _Assign Trader vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/AssignTrader.cls


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
    
                request = service.createRequest("AssignTrader")
                
                request.append("EMSX_SEQUENCE", 3744303)
                request.append("EMSX_SEQUENCE", 3744341)

                request.set("EMSX_ASSIGNEE_TRADER_UUID", 12109783)
            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Output:- Without proper ``EMBR<GO>`` permssion.


.. code-block:: none

    C:\Users\tckim\OneDrive\_scripts>py -3 AssignTrader.py
    Bloomberg - EMSX API Example - AssignTrader
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: AssignTrader = {
        EMSX_SEQUENCE[] = {
            4733955
        }
        EMSX_ASSIGNEE_TRADER_UUID = 7569479
    }

    Processing RESPONSE event
    MESSAGE: ErrorInfo = {
        ERROR_CODE = 96233
        ERROR_MESSAGE = "Not Authorized"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: ErrorInfo
    ERROR CODE: 96233       ERROR MESSAGE: Not Authorized
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Broker Spec Request
=====================
The ``BrokerSpec`` request allows EMSX API users to call all the production broker strategy name and fields and FIX tags 
associated with the broker strategies. Unfortunately, this is currently only available for production broker strategy 
fields. The service name is ``\\blp\emsx.brokerspec``.


Full code sample:-

==================== ================= ==================
`Broker Spec cpp`_   `Broker Spec cs`_ `Broker Spec vba`_ 
-------------------- ----------------- ------------------
`Broker Spec java`_  `Broker Spec py`_
==================== ================= ==================

.. _Broker Spec cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/BrokerSpec.cpp

.. _Broker Spec cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/BrokerSpec.cs

.. _Broker Spec java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/AssignTrader.java

.. _Broker Spec py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/BrokerSpec.py

.. _Broker Spec vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/BrokerSpec.cls


Call ``//blp/emsx.brokerspec`` service:-


.. code-block:: python
  
        
    SESSION_STARTED         = blpapi.Name("SessionStarted")
    SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
    SERVICE_OPENED          = blpapi.Name("ServiceOpened")
    SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
    ERROR_INFO              = blpapi.Name("ErrorInfo")
    BROKER_SPEC             = blpapi.Name("BrokerSpec")

    d_service="//blp/emsx.brokerspec" # The BrokerSpec service is only available in the production environment
    d_host="localhost"
    d_port=8194
    bEnd=False


Specify the UUID:-


.. code-block:: python

    
    def processServiceStatusEvent(self,event,session):
        print "Processing SERVICE_STATUS event"
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print "Service opened..."

                service = session.getService(d_service)
    
                request = service.createRequest("GetBrokerSpecForUuid")

                request.set("uuid", 8049857)

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Get broker code, strategy name, and strategy parameters


.. code-block:: python

                        brokers=msg.getElement("brokers")
                    
                    num = brokers.numValues()
                    
                    print "Number of Brokers: %d\n" % (num)
                    
                    for broker in brokers.values():
                        code = broker.getElement("code").getValue()
                        assetClass = broker.getElement("assetClass").getValue()
                        
                        if broker.hasElement("strategyFixTag"):
                            tag = broker.getElement("strategyFixTag").getValue()
                            print "\nBroker code: %s\tclass: %s\ttag: %s" % (code,assetClass,tag)
                            strats = broker.getElement("strategies")
                            numStrats = strats.numValues()
                            print"\tNo. of Strategies: %d" % (numStrats)
                            for strat in strats.values():
                                name = strat.getElement("name").getValue()
                                fixVal = strat.getElement("fixValue").getValue()
                                print "\n\tStrategy Name: %s\tFix Value: %s" % (name,fixVal)
                                
                                parameters = strat.getElement("parameters")
                                
                                numParams = parameters.numValues()
                                
                                print "\t\tNo. of Parameters: %d\n" % (numParams)
                                
                                for param in parameters.values():
                                    pname = param.getElement("name").getValue()
                                    tag = param.getElement("fixTag").getValue()
                                    required = param.getElement("isRequired").getValue()
                                    replaceable = param.getElement("isReplaceable").getValue()
                                    print "\t\tParameter: %s\tTag: %d\tRequired: %s\tReplaceable: %s" % (pname,tag,required,replaceable)
                                    
                                    typeName = param.getElement("type").getElement(0).name()
                                    
                                    vals = ""
                                    
                                    if typeName=="enumeration":
                                        
                                        enumerators = param.getElement("type").getElement(0).getElement("enumerators")
                                        
                                        for enum in enumerators.values():
                                            vals = vals + enum.getElement("name").getValue() + "[" + enum.getElement("fixValue").getValue() + "],"

                                        if len(vals) > 0: vals = vals[:-1]
                                        
                                    
                                    elif typeName=="range":
                                        rng = param.getElement("type").getElement(0)
                                        mn = rng.getElement("min").getValue()
                                        mx = rng.getElement("max").getValue()
                                        st = rng.getElement("step").getValue()
                                        vals = "min:%d max:%d step:%d" % (mn,mx,st)
                                        
                                        
                                    elif typeName=="string":
                                        possVals = param.getElement("type").getElement(0).getElement("possibleValues")
                                        
                                        
                                        for val in possVals.values():
                                            vals = vals + val +","
                                            
                                        if len(vals) > 0: vals = vals[:-1]
                                        
                                    
                                    if len(vals) > 0:
                                        print "\t\t\tType: %s (%s)" % (typeName, vals)           
                                    else:
                                        print "\t\t\tType: %s" % (typeName)           

                        else:
                            print "\nBroker code: %s\tclass: %s" % (code,assetClass)
                            print"\tNo strategies\n"


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 BrokerSpec.py
    Bloomberg - EMSX API Example - BrokerSpec
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: GetBrokerSpecForUuid = {
        uuid = 6767714
    }

    Processing RESPONSE event
    MESSAGE TYPE: BrokerSpec
    Number of Brokers: 20


    Broker code: BB class: Equity   tag: 9002
            No. of Strategies: 10

            Strategy Name: NONE     Fix Value: NONE
                    No. of Parameters: 0


            Strategy Name: VWAP     Fix Value: VWAP
                    No. of Parameters: 0


            Strategy Name: PARTICIPATE      Fix Value: PART
                    No. of Parameters: 0


            Strategy Name: INLINE   Fix Value: INLINE
                    No. of Parameters: 0


            Strategy Name: ARRIVAL PRICE    Fix Value: ARRP
                    No. of Parameters: 0


            Strategy Name: BIPS     Fix Value: 2
                    No. of Parameters: 0


            Strategy Name: EP_PE    Fix Value: EP
                    No. of Parameters: 0


            Strategy Name: PAIRS STRATEGY   Fix Value: PAIR
                    No. of Parameters: 0


            Strategy Name: BEST EX  Fix Value: BEST-EX
                    No. of Parameters: 0


            Strategy Name: ratest   Fix Value: ratest
                    No. of Parameters: 0

            Time In Force:
                    Name: DAY       Fix Value: 0
                    Name: FOK       Fix Value: 4
                    Name: GTC       Fix Value: 1
                    Name: GTD       Fix Value: 6

            Order Types:
                    Name: LMT       Fix Value: 2
                    Name: MKT       Fix Value: 1
                    Name: SL        Fix Value: 4
                    Name: ST        Fix Value: 3

            Handling Instructions:
                    Name: ANY       Fix Value: 2
                    Name: Auto      Fix Value: 1
                    Name: DMA       Fix Value: 4
                    Name: MAN       Fix Value: 3
                    Name: ORD       Fix Value: 0

    Broker code: BB class: Option
            No strategies

            Time In Force:
                    Name: DAY       Fix Value: 0

            Order Types:
                    Name: LMT       Fix Value: 2
                    Name: MKT       Fix Value: 1

            Handling Instructions:
                    Name: ANY       Fix Value: 2
                    Name: AUTO      Fix Value: 1
                    Name: MAN       Fix Value: 3

    Broker code: EFIX       class: Equity   tag: 6005
            No. of Strategies: 53

            Strategy Name: TSTRIKE1 Fix Value: 2
                    No. of Parameters: 0


            Strategy Name: INLINE   Fix Value: INLINE
                    No. of Parameters: 0


            Strategy Name: STRATEGY8        Fix Value: 8
                    No. of Parameters: 0


            Strategy Name: STRATEGY9        Fix Value: 9
                    No. of Parameters: 0


            Strategy Name: STRATEGY10       Fix Value: 10
                    No. of Parameters: 0


            Strategy Name: STRATEGY11       Fix Value: 11
                    No. of Parameters: 0


            Strategy Name: STRATEGY12       Fix Value: 12
                    No. of Parameters: 0


            Strategy Name: STRATEGY13       Fix Value: 13
                    No. of Parameters: 0


            Strategy Name: STRATEGY14       Fix Value: 14
                    No. of Parameters: 0


            Strategy Name: STRATEGY15       Fix Value: 15
                    No. of Parameters: 0


            Strategy Name: STRATEGY16       Fix Value: 16
                    No. of Parameters: 0


            Strategy Name: STRATEGY17       Fix Value: 17
                    No. of Parameters: 0


            Strategy Name: STRATEGY18       Fix Value: 18
                    No. of Parameters: 0


            Strategy Name: STRATEGY19       Fix Value: 19
                    No. of Parameters: 0


            Strategy Name: STRATEGY20       Fix Value: 20
                    No. of Parameters: 0


            Strategy Name: STRATEGY21       Fix Value: 21
                    No. of Parameters: 0


            Strategy Name: STRATEGY22       Fix Value: 22
                    No. of Parameters: 0


            Strategy Name: STRATEGY23       Fix Value: 23
                    No. of Parameters: 0


            Strategy Name: STRATEGY24       Fix Value: 24
                    No. of Parameters: 0


            Strategy Name: STRATEGY25       Fix Value: 25
                    No. of Parameters: 0


            Strategy Name: Merge    Fix Value: Merge
                    No. of Parameters: 0


            Strategy Name: VWAP     Fix Value: GVW3
                    No. of Parameters: 0


            Strategy Name: TWAP     Fix Value: GTW3
                    No. of Parameters: 0


            Strategy Name: VP       Fix Value: GVP3
                    No. of Parameters: 0


            Strategy Name: VWAP2    Fix Value: 3
                    No. of Parameters: 0


            Strategy Name: ABC      Fix Value: 4
                    No. of Parameters: 0


            Strategy Name: TIME TEST        Fix Value: 1
                    No. of Parameters: 0


            Strategy Name: TIME TEST1       Fix Value: 40
                    No. of Parameters: 0


            Strategy Name: strategy 29      Fix Value: L
                    No. of Parameters: 0


            Strategy Name: strategy 30      Fix Value: 30
                    No. of Parameters: 0


            Strategy Name: ALGOT    Fix Value: TT
                    No. of Parameters: 0


            Strategy Name: Mike Sat Morning Fix Value: M3
                    No. of Parameters: 0


            Strategy Name: janurary Fix Value: jan
                    No. of Parameters: 0


            Strategy Name: test33           Fix Value: 10114
                    No. of Parameters: 0


            Strategy Name: iceberg  Fix Value: iceberg
                    No. of Parameters: 0


            Strategy Name: Merge2   Fix Value: Merge2
                    No. of Parameters: 0


            Strategy Name: testwf   Fix Value: testwf
                    No. of Parameters: 0


            Strategy Name: TS Strike        Fix Value: y
                    No. of Parameters: 0


            Strategy Name: TS Strike        Fix Value: y
                    No. of Parameters: 0


            Strategy Name: strategy 30      Fix Value: 30
                    No. of Parameters: 0


            Strategy Name: Strategy 30      Fix Value: 30
                    No. of Parameters: 0


            Strategy Name: INLIN    Fix Value: INLINE
                    No. of Parameters: 0


            Strategy Name: TS Strike        Fix Value: y
                    No. of Parameters: 0


            Strategy Name: Strategy 30      Fix Value: 30
                    No. of Parameters: 0


            Strategy Name: SMART    Fix Value: SMART
                    No. of Parameters: 0


            Strategy Name: y029test Fix Value: 1029
                    No. of Parameters: 0


            Strategy Name: ra_test  Fix Value: ratest
                    No. of Parameters: 0


            Strategy Name: DEMO     Fix Value: D
                    No. of Parameters: 0


            Strategy Name: A        Fix Value: 2
                    No. of Parameters: 0


            Strategy Name: TEST1    Fix Value: T1
                    No. of Parameters: 0


            Strategy Name: TEST2    Fix Value: T2
                    No. of Parameters: 0


            Strategy Name: TEST3    Fix Value: T3
                    No. of Parameters: 0


            Strategy Name: jeff     Fix Value: jeff
                    No. of Parameters: 0

            Time In Force:
                    Name: CLO       Fix Value: 7
                    Name: DAY       Fix Value: 0
                    Name: FOK       Fix Value: 4
                    Name: GTC       Fix Value: 1
                    Name: GTD       Fix Value: 6
                    Name: GTX       Fix Value: 5
                    Name: IOC       Fix Value: 3
                    Name: OPG       Fix Value: A

            Order Types:
                    Name: CD        Fix Value: Q
                    Name: COVR      Fix Value: F
                    Name: FUN       Fix Value: I
                    Name: JP        Fix Value: N
                    Name: LMT       Fix Value: 2
                    Name: LOB       Fix Value: R
                    Name: LOC       Fix Value: B
                    Name: LOO       Fix Value: 6
                    Name: MKT       Fix Value: 1
                    Name: MOC       Fix Value: 5
                    Name: MOO       Fix Value: X
                    Name: OC        Fix Value: A
                    Name: PEGG      Fix Value: P
                    Name: RED       Fix Value: E
                    Name: SL        Fix Value: 4
                    Name: ST        Fix Value: 3

            Handling Instructions:
                    Name: ANY       Fix Value: 2
                    Name: AUTO      Fix Value: 1
                    Name: MAN       Fix Value: 3

    Broker code: EFIX       class: Future   tag: 1000
            No. of Strategies: 6

            Strategy Name: test 2   Fix Value: 200
                    No. of Parameters: 0


            Strategy Name: test     Fix Value: 100
                    No. of Parameters: 0


            Strategy Name: time test        Fix Value: time
                    No. of Parameters: 0


            Strategy Name: Range test       Fix Value: rng
                    No. of Parameters: 0


            Strategy Name: test3    Fix Value: I
                    No. of Parameters: 0


            Strategy Name: DEMO     Fix Value: D
                    No. of Parameters: 0

            Time In Force:
                    Name: DAY       Fix Value: 0
                    Name: GTC       Fix Value: 1
                    Name: GTD       Fix Value: 6
                    Name: GTI       Fix Value: 8
                    Name: GTT       Fix Value: 9
                    Name: IOC       Fix Value: 3

            Order Types:
                    Name: LMT       Fix Value: 2
                    Name: MKT       Fix Value: 1
                    Name: MOC       Fix Value: 5
                    Name: SL        Fix Value: 4
                    Name: ST        Fix Value: 3

            Handling Instructions:
                    Name: ANY       Fix Value: 2
                    Name: AUTO      Fix Value: 1
                    Name: DOT       Fix Value: 4
                    Name: MAN       Fix Value: 3
    ...

Cancel Order Extended Request
=============================
In ``EMSX<GO>`` there is a feature that allows the user to cancel the parent order and child routes associated with the parent order in a single call. The ``CancelOrderEx`` request replicates this ``EMSX<GO>`` UI feature.

However, unlike the `CancelRouteEx`_ request which changes the parent order state into ``Assigned``, this request will permanently place the order in an inoperable  ``Cancel`` state. 

Full code sample:-

===================== =================== ===================
`Cancel Order cs`_    `Cancel Order py`_	
--------------------- ------------------- -------------------

===================== =================== ===================

.. _Cancel Order cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CancelOrder.cs

.. _Cancel Order py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CancelOrderEx.py

.. _CancelRouteEx: https://emsx-api-doc.readthedocs.io/en/latest/programmable/requestResponse.html#cancel-route-extended-request

.. hint:: 

	Please right click on the top code sample link to open in a new tab.


Cancel Route Extended Request
==============================
In ``EMSX<GO>`` we have a notion of parent order and child routes. The ``CancelRoute`` request is to effectively send out 
a cancellation request to the execution venue of the current live route. Submission of ``CancelRoute`` does not 
automatically cancel the outstanding route. This action needs to be acknowledged and performed by the execution venue 
of the route.


Full code sample:-

===================== =================== ===================
`Cancel Route cpp`_   `Cancel Route cs`_  `Cancel Route vba`_	
--------------------- ------------------- -------------------
`Cancel Route java`_  `Cancel Route py`_
===================== =================== ===================

.. _Cancel Route cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/CancelRoute.cpp

.. _Cancel Route cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CancelRoute.cs

.. _Cancel Route java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/CancelRoute.java

.. _Cancel Route py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CancelRoute.py

.. _Cancel Route vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/CancelRoute.cls


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
    
                request = service.createRequest("CancelRoute")

                #request.set("EMSX_REQUEST_SEQ", 1)
                #request.set("EMSX_TRADER_UUID", 1234567)        # UUID of trader who owns the order

                routes = request.getElement("ROUTES")
    
                route = routes.appendElement()
                route.getElement("EMSX_SEQUENCE").setValue(3744354)
                route.getElement("EMSX_ROUTE_ID").setValue(1)
            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        
	                	 

Output:-

.. code-block:: none
    
    C:\Users\tckim\OneDrive\_scripts>py -3 CancelOrderEx.py
    Bloomberg - EMSX API Example - CancelOrderEx
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: CancelOrderEx = {
        EMSX_SEQUENCE[] = {
            4733955
        }
    }

    Processing RESPONSE event
    MESSAGE: CancelOrderEx = {
        STATUS = 1
        MESSAGE = "Order cancellation request sent to broker"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: CancelOrderEx
    STATUS: 1       MESSAGE: Order cancellation request sent to broker
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Create Basket Request
=====================
Creating a basket requires the user to create a request from the service object of type ``CreateBasket`` and fill in the required fields before submitting the request.

The ``CreateBasket`` request creates a basket with the list of securities. This maintains a list or a basket from a portfolio perspective.

Currently, in EMSX API this is a two-step process.

The first step is for the user to use ``CreateOrder`` request to create the orders and capture the ``EMSX_SEQUENCE`` from the response message. 

The second step is to include the ``EMSX_SEQUENCE`` number inside an array to add the orders into a basket and use the ``EMSX_BASKET_NAME`` element in the ``CreateBasket`` request to specify the name of the basket. 


Full code sample:-

===================== =================== ====================
`Create Basket cpp`_  `Create Basket cs`_ `Create Basket vba`_   
--------------------- ------------------- --------------------
`Create Basket java`_ `Create Basket py`_
===================== =================== ====================


.. _Create Basket cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/CreateBasket.cpp

.. _Create Basket cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CreateBasket.cs

.. _Create Basket vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/CreateBasket.cls

.. _Create Basket java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/CreateBasket.java

.. _Create Basket py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CreateBasket.py


.. hint:: 

    Please right click on the top code sample link to open in a new tab.

.. code-block:: python
   :linenos:

     def processServiceStatusEvent(self,event,session):
        print("Processing SERVICE_STATUS event")
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print("Service opened...")

                service = session.getService(d_service)
    
                request = service.createRequest("CreateBasket")

                # define the basket name
                request.set("EMSX_BASKET_NAME", "TestBasket")

                # add any number of orders
                request.append("EMSX_SEQUENCE", 4313227)
                request.append("EMSX_SEQUENCE", 4313228)
                #request.append("EMSX_SEQUENCE", 4313184)

                print("Request: %s" % request.toString())
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                    
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print("Error: Service failed to open")
                     

Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 CreateBasket.py
    Bloomberg - EMSX API Example - CreateBasket
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: CreateBasket = {
        EMSX_BASKET_NAME = "TestBasket"
        EMSX_SEQUENCE[] = {
            4733961, 4733962
        }
    }

    Processing RESPONSE event
    MESSAGE: CreateBasket = {
        EMSX_SEQUENCE[] = {
            4733961, 4733962
        }
        MESSAGE = "Orders added to Basket"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: CreateBasket
    EMSX_SEQUENCE: 4733961  MESSAGE: Orders added to Basket
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Create Order Request
====================
Creating an order requires the user to create a request from the service object of type ``CreateOrder`` and fill in the required fields before submitting the request. 

If the handling instruction is for DMA access or any other non-standard handling instructions, EMSX API will not allow users to stage the order from the EMSX API unless the broker enables the broker code for EMSX API.  This is also true for custom Time in Force fields. Any non-standard TIF will also be restricted from staging unless the broker enables the broker code for EMSX API.


Full code sample:-

===================== =================== ===================
`Create Order cpp`_   `Create Order cs`_  `Create Order vba`_	
--------------------- ------------------- -------------------
`Create Order java`_  `Create Order py`_
===================== =================== ===================

.. _Create Order cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/CreateOrder.cpp

.. _Create Order cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CreateOrder.cs

.. _Create Order java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/CreateOrder.java

.. _Create Order py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CreateOrder.py

.. _Create Order vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/CreateOrder.cls


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
                #request.set("EMSX_RELEASE_TIME", 1259)
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


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 CreateOrder.py
    Bloomberg - EMSX API Example - CreateOrder
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: CreateOrder = {
        EMSX_TICKER = "MSFT US Equity"
        EMSX_AMOUNT = 1100
        EMSX_ORDER_TYPE = MKT
        EMSX_TIF = DAY
        EMSX_HAND_INSTRUCTION = "ANY"
        EMSX_SIDE = BUY
    }

    Processing RESPONSE event
    MESSAGE: CreateOrder = {
        EMSX_SEQUENCE = 4733955
        MESSAGE = "Order created"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: CreateOrder
    EMSX_SEQUENCE: 4733955  MESSAGE: Order created
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Create Order and Route Extended Request
=======================================
The ``CreateOrderAndRouteEx`` request can be used for both strategy and non-strategy broker destinations.  Creating 
an order and routing with strategy requires the user to create a request from the service object of type ``
CreateOrderAndRouteEx`` and fill in the required fields before submitting the request. 


.. note:: 

	The user will first need to use various ``Get___`` requests to obtain all the necessary information to use the broker strategies the user is enabled for, returned in response. Subsequently, the user can then request ``GetBrokerStrategiesWithAssetClass`` to get all the broker strategies user is enabled for that particular broker code and asset class. 

    Lastly, ``GetBrokerStrategyInfoWithAssetClass`` will get all the fields for the provided broker strategy in the particular order in which they need to be submitted in ``CreateOrderAndRouteEx`` and ``RouteEx`` requests.


Full code sample:-

======================================= ===================================== ======================================
`Create Order And Route Extended cpp`_  `Create Order And Route Extended cs`_ `Create Order And Route Extended vba`_	
--------------------------------------- ------------------------------------- --------------------------------------
`Create Order And Route Extended java`_ `Create Order And Route Extended py`_
======================================= ===================================== ======================================

.. _Create Order And Route Extended cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/CreateOrderAndRouteEx.cpp

.. _Create Order And Route Extended cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CreateOrderAndRouteEx.cs

.. _Create Order And Route Extended java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/CreateOrderAndRouteEx.java

.. _Create Order And Route Extended py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CreateOrderAndRouteEx.py

.. _Create Order And Route Extended vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/CreateOrderAndRouteEx.cls


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


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 CreateOrderAndRouteEx.py
    Bloomberg - EMSX API Example - CreateOrderAndRouteEx
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: CreateOrderAndRouteEx = {
        EMSX_TICKER = "FB US Equity"
        EMSX_AMOUNT = 1000
        EMSX_ORDER_TYPE = MKT
        EMSX_TIF = DAY
        EMSX_HAND_INSTRUCTION = "ANY"
        EMSX_SIDE = SELL
        EMSX_BROKER = "BMTB"
        EMSX_ACCOUNT = "testAccount"
        EMSX_NOTES = "blah blah blah"
        EMSX_ORDER_REF_ID = "UniqueID"
        EMSX_P_A = "A"
        EMSX_ROUTE_REF_ID = "UniqueID2"
        EMSX_STRATEGY_PARAMS = {
            EMSX_STRATEGY_NAME = "VWAP"
            EMSX_STRATEGY_FIELD_INDICATORS[] = {
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 0
                }
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 0
                }
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 1
                }
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 1
                }
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 1
                }
                EMSX_STRATEGY_FIELD_INDICATORS = {
                    EMSX_FIELD_INDICATOR = 1
                }
            }
            EMSX_STRATEGY_FIELDS[] = {
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = "09:30:00"
                }
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = "10:30:00"
                }
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = ""
                }
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = ""
                }
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = ""
                }
                EMSX_STRATEGY_FIELDS = {
                    EMSX_FIELD_DATA = ""
                }
            }
        }
    }

    Processing RESPONSE event
    MESSAGE: CreateOrderAndRouteEx = {
        EMSX_SEQUENCE = 4733965
        EMSX_ROUTE_ID = 1
        MESSAGE = "Order created and routed"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: CreateOrderAndRouteEx
    EMSX_SEQUENCE: 4733965  EMSX_ROUTE_ID: 1        MESSAGE: Order created and routed
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Create Order And Route Manually Request
=======================================
The ``CreateOrderAndRouteManually`` request is generally used for phone orders where the placement is external to EMSX API. This request creates an order and notifies EMSX<GO> that this order is routed to the execution venue.


Full code sample:-

======================================= ===================================== ======================================
`Create Order And Route Manually cpp`_  `Create Order And Route Manually cs`_ `Create Order And Route Manually vba`_	
--------------------------------------- ------------------------------------- --------------------------------------
`Create Order And Route Manually java`_ `Create Order And Route Manually py`_
======================================= ===================================== ======================================


.. _Create Order And Route Manually cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/CreateOrderAndRouteManually.cpp

.. _Create Order And Route Manually cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/CreateOrderAndRouteManually.cs

.. _Create Order And Route Manually java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/CreateOrderAndRouteManually.java

.. _Create Order And Route Manually py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/CreateOrderAndRouteManually.py

.. _Create Order And Route Manually vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/CreateOrderAndRouteManually.cls


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
                #request.set("EMSX_RELEASE_TIME", 1259)
                #request.set("EMSX_REQUEST_SEQ", 1001)
                #request.set("EMSX_SETTLE_DATE", 20170106)
                #request.set("EMSX_STOP_PRICE", 123.5)

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Delete Order Request
====================
The ``DeleteOrder`` request deletes an existing order in EMSX<GO>. This is not the same action as canceling the parent order. In fact, EMSX API does not expose Cancel Order status as in EMSX<GO>. 

The primary reason behind this is because the cancel rrder in EMSX<GO> really just puts an order in an inoperable state and doesn't really serve any meaningful function.


Full code sample:-

==================== =================== ===================
`Delete Order cpp`_  `Delete Order cs`_  `Delete Order vba`_	
-------------------- ------------------- -------------------
`Delete Order java`_ `Delete Order py`_
==================== =================== ===================


.. _Delete Order cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/DeleteOrder.cpp

.. _Delete Order cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/DeleteOrder.cs

.. _Delete Order java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/DeleteOrder.java

.. _Delete Order py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/DeleteOrder.py

.. _Delete Order vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/DeleteOrder.cls


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
    
                request = service.createRequest("DeleteOrder")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                request.getElement("EMSX_SEQUENCE").appendValue(3744363)
                request.getElement("EMSX_SEQUENCE").appendValue(3744364)

            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        
                  

Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 DeleteOrder.py
    Bloomberg - EMSX API Example - DeleteOrder
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: DeleteOrder = {
        EMSX_SEQUENCE[] = {
            4733961
        }
    }

    Processing RESPONSE event
    MESSAGE: DeleteOrder = {
        STATUS = 0
        MESSAGE = "Order deleted"
    }

    CORRELATION ID: 3
    MESSAGE TYPE: DeleteOrder
    STATUS: 0       MESSAGE: Order deleted
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Get All Field Metadata Request
==============================
The ``GetAllFiedlMetaData`` request provides all field metadata in a response message.


Full code sample:-

=============================== ============================= ==============================
`Get All Field Meta Data cpp`_ 	`Get All Field Meta Data cs`_ `Get All Field Meta Data vba`_	
------------------------------- ----------------------------- ------------------------------
`Get All Field Meta Data java`_ `Get All Field Meta Data py`_
=============================== ============================= ==============================

.. _Get All Field Meta Data cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetAllFieldMetaData.cpp

.. _Get All Field Meta Data cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetAllFieldMetaData.cs

.. _Get All Field Meta Data java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetAllFieldMetaData.java

.. _Get All Field Meta Data py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetAllFieldMetaData.py

.. _Get All Field Meta Data vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetAllFieldMetaData.cls


.. hint:: 

	Please right click on the top code sample link to open in a new tab.


.. code-block:: python


    def processServiceStatusEvent(self,event,session):
        print "Processing SERVICE_STATUS event"
        
        for msg in event:
            
            if msg.messageType() == SERVICE_OPENED:
                print "Service opened..."

                service = session.getService(d_service)
    
                request = service.createRequest("GetAllFieldMetaData")

                #request.set("EMSX_REQUEST_SEQ", 1)
            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Process response messages:-


.. code-block:: python


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
                elif msg.messageType() == GET_ALL_FIELD_METADATA:

                    md = msg.getElement("MetaData")
                    
                    for e in md.values():
                        
                        emsx_field_name = e.getElementAsString("EMSX_FIELD_NAME")
                        emsx_disp_name = e.getElementAsString("EMSX_DISP_NAME")
                        emsx_type = e.getElementAsString("EMSX_TYPE")
                        emsx_level = e.getElementAsInteger("EMSX_LEVEL")
                        emsx_len = e.getElementAsInteger("EMSX_LEN")
                        
                        print "MetaData: %s,%s,%s,%d,%d" % (emsx_field_name, emsx_disp_name, emsx_type, emsx_level, emsx_len)

                global bEnd
                bEnd = True
                
    def processMiscEvents(self, event):
        
        print "Processing " + event.eventType() + " event"
        
        for msg in event:

            print "MESSAGE: %s" % (msg.tostring())


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 GetAllFieldMetaData.py
    Bloomberg - EMSX API Example - GetAllFieldMetaData
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: GetAllFieldMetaData = {
    }

    Processing RESPONSE event
    MESSAGE: GetAllFieldMetaData = {
        MetaData[] = {
            MetaData = {
                EMSX_FIELD_NAME = "MSG_TYPE"
                EMSX_DISP_NAME = "Msg Type"
                EMSX_TYPE = String
                EMSX_LEVEL = 0
                EMSX_LEN = 1
            }
            MetaData = {
                EMSX_FIELD_NAME = "MSG_SUB_TYPE"
                EMSX_DISP_NAME = "Msg Sub Type"
                EMSX_TYPE = String
                EMSX_LEVEL = 0
                EMSX_LEN = 1
            }
            MetaData = {
                EMSX_FIELD_NAME = "EVENT_STATUS"
                EMSX_DISP_NAME = "Msg Status"
                EMSX_TYPE = Int32
                EMSX_LEVEL = 0
                EMSX_LEN = 10
            }
            MetaData = {
                EMSX_FIELD_NAME = "API_SEQ_NUM"
                EMSX_DISP_NAME = "Api Sequence"
                EMSX_TYPE = Int64
                EMSX_LEVEL = 0
                EMSX_LEN = 20
            }
            MetaData = {
                EMSX_FIELD_NAME = "EMSX_SEQUENCE"
                EMSX_DISP_NAME = "Sequence #"
                EMSX_TYPE = Int32
                EMSX_LEVEL = 27
                EMSX_LEN = 10
            }
            MetaData = {
                EMSX_FIELD_NAME = "EMSX_ROUTE_ID"
                EMSX_DISP_NAME = "Tran No"
                EMSX_TYPE = Int32
                EMSX_LEVEL = 11
                EMSX_LEN = 10
            }
            MetaData = {
                EMSX_FIELD_NAME = "EMSX_FILL_ID"
                EMSX_DISP_NAME = "Fill Id"
                EMSX_TYPE = Int32
                EMSX_LEVEL = 2
                EMSX_LEN = 10
            }
            MetaData = {
                EMSX_FIELD_NAME = "EMSX_SIDE"
                EMSX_DISP_NAME = "B/S"
                EMSX_TYPE = String
                EMSX_LEVEL = 17
                EMSX_LEN = 4
           ...

           ...
            MetaData = {
                EMSX_FIELD_NAME = "EMSX_LEG_FILL_TICKER"
                EMSX_DISP_NAME = "Leg Fill Ticker"
                EMSX_TYPE = String
                EMSX_LEVEL = 2
                EMSX_LEN = 32
            }
        }
    }

    CORRELATION ID: 3
    MESSAGE TYPE: GetAllFieldMetaData
    MetaData: MSG_TYPE,Msg Type,String,0,1
    MetaData: MSG_SUB_TYPE,Msg Sub Type,String,0,1
    MetaData: EVENT_STATUS,Msg Status,Int32,0,10
    MetaData: API_SEQ_NUM,Api Sequence,Int64,0,20
    MetaData: EMSX_SEQUENCE,Sequence #,Int32,27,10
    ...
    ...
    MetaData: EMSX_ROUTE_AS_OF_TIME_MICROSEC,Route As of Time,Time,2,20
    MetaData: EMSX_AS_OF_DATE,Order/Route As of Date,Date,24,8
    MetaData: EMSX_AS_OF_TIME_MICROSEC,Order/Route As of Time,Time,24,20
    MetaData: EMSX_LEG_FILL_SIDE,Leg Fill Side,String,2,3
    MetaData: EMSX_LEG_FILL_DATE_ADDED,Leg Fill Date Added,Date,2,8
    MetaData: EMSX_LEG_FILL_TIME_ADDED,Leg fill Time Added,Time,2,20
    MetaData: EMSX_LEG_FILL_SHARES,Leg Fill Shares,Double,2,15
    MetaData: EMSX_LEG_FILL_PRICE,Leg Fill Price,Double,2,15
    MetaData: EMSX_LEG_FILL_SEQ_NO,Leg Fill Seq No,Int32,2,10
    MetaData: EMSX_LEG_FILL_TICKER,Leg Fill Ticker,String,2,32
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Get Broker Strategies with Asset Class Request
==============================================
The ``GetBrokerStrategiesWithAssetClass`` request provides all broker strategy fields with asset class data in a response message.


Full code sample:-

============================================== ============================================= =============================================
`Get Broker Strategies With Asset Class cpp`_ 	`Get Broker Strategies With Asset Class cs`_ `Get Broker Strategies With Asset Class vba`_	
---------------------------------------------- --------------------------------------------- ---------------------------------------------
`Get Broker Strategies With Asset Class java`_ 	`Get Broker Strategies With Asset Class py`_
============================================== ============================================= =============================================

.. _Get Broker Strategies With Asset Class cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetBrokerStrategiesWithAssetClass.cpp

.. _Get Broker Strategies With Asset Class cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetBrokerStrategiesWithAssetClass.cs

.. _Get Broker Strategies With Asset Class java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetBrokerStrategiesWithAssetClass.java

.. _Get Broker Strategies With Asset Class py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetBrokerStrategiesWithAssetClass.py

.. _Get Broker Strategies With Asset Class vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetBrokerStrategiesWithAssetClass.cls


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
    
                request = service.createRequest("GetBrokerStrategiesWithAssetClass")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                request.set("EMSX_ASSET_CLASS","EQTY")  # one of EQTY, OPT, FUT or MULTILEG_OPT
                request.set("EMSX_BROKER","BMTB")
            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 GetBrokerStrategiesWithAssetClass.py
    Bloomberg - EMSX API Example - GetBrokerStrategiesWithAssetClass
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: GetBrokerStrategiesWithAssetClass = {
        EMSX_ASSET_CLASS = EQTY
        EMSX_BROKER = "PAIR"
    }

    Processing RESPONSE event
    MESSAGE: GetBrokerStrategiesWithAssetClass = {
        EMSX_STRATEGIES[] = {
            ""
        }
    }

    CORRELATION ID: 3
    MESSAGE TYPE: GetBrokerStrategiesWithAssetClass
    EMSX_STRATEGY:
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Get Broker Strategy Info with Asset Class Request
=================================================
The ``GetBrokerStrategyInfoWithAssetClass`` request provides all broker strategy information fields with asset classdata in a response message.


Full code sample:-

================================================= ================================================ ================================================
`Get Broker Strategy Info With Asset Class cpp`_  `Get Broker Strategy Info With Asset Class cs`_  `Get Broker Strategy Info With Asset Class vba`_	
------------------------------------------------- ------------------------------------------------ ------------------------------------------------
`Get Broker Strategy Info With Asset Class java`_ `Get Broker Strategy Info With Asset Class py`_
================================================= ================================================ ================================================

.. _Get Broker Strategy Info With Asset Class cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetBrokerStrategyInfoWithAssetClass.cpp

.. _Get Broker Strategy Info With Asset Class cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetBrokerStrategyInfoWithAssetClass.cs

.. _Get Broker Strategy Info With Asset Class java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetBrokerStrategyInfoWithAssetClass.java

.. _Get Broker Strategy Info With Asset Class py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetBrokerStrategyInfoWithAssetClass.py

.. _Get Broker Strategy Info With Asset Class vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetBrokerStrategyInfoWithAssetClass.cls


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
    
                request = service.createRequest("GetBrokerStrategyInfoWithAssetClass")

                request.set("EMSX_REQUEST_SEQ", 1)
                
                request.set("EMSX_ASSET_CLASS","EQTY")  # one of EQTY, OPT, FUT or MULTILEG_OPT
                request.set("EMSX_BROKER","BMTB")
                request.set("EMSX_STRATEGY","VWAP")
                    
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        	    


Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 GetBrokerStrategyInfoWithAssetClass.py
    Bloomberg - EMSX API Example - GetBrokerStrategyInfoWithAssetClass
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: GetBrokerStrategyInfoWithAssetClass = {
        EMSX_REQUEST_SEQ = 1
        EMSX_ASSET_CLASS = EQTY
        EMSX_BROKER = "BMTB"
        EMSX_STRATEGY = "VWAP"
    }

    Processing RESPONSE event
    MESSAGE: GetBrokerStrategyInfoWithAssetClass = {
        EMSX_STRATEGY_INFO[] = {
            EMSX_STRATEGY_INFO = {
                FieldName = "Start Time"
                Disable = 0
                StringValue = ""
            }
            EMSX_STRATEGY_INFO = {
                FieldName = "End Time"
                Disable = 0
                StringValue = ""
            }
            EMSX_STRATEGY_INFO = {
                FieldName = "Max % Volume"
                Disable = 0
                StringValue = ""
            }
            EMSX_STRATEGY_INFO = {
                FieldName = "Discretion"
                Disable = 0
                StringValue = ""
            }
            EMSX_STRATEGY_INFO = {
                FieldName = "Display Qty"
                Disable = 0
                StringValue = ""
            }
            EMSX_STRATEGY_INFO = {
                FieldName = "FltLmtType"
                Disable = 0
                StringValue = ""
            }
        }
    }

    CORRELATION ID: 3
    MESSAGE TYPE: GetBrokerStrategyInfoWithAssetClass
    EMSX_STRATEGY_INFO: Start Time, 0,
    EMSX_STRATEGY_INFO: End Time, 0,
    EMSX_STRATEGY_INFO: Max % Volume, 0,
    EMSX_STRATEGY_INFO: Discretion, 0,
    EMSX_STRATEGY_INFO: Display Qty, 0,
    EMSX_STRATEGY_INFO: FltLmtType, 0,
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Get Brokers with Asset Class Request
====================================
The ``GetBrokersWithAssetClass`` request provides all broker information with asset class data in a response message.


Full code sample:-

==================================== ==================================== ===================================
`Get Brokers With Asset Class cpp`_  `Get Brokers With Asset Class cs`_   `Get Brokers With Asset Class vba`_	
------------------------------------ ------------------------------------ -----------------------------------
`Get Brokers With Asset Class java`_ `Get Brokers With Asset Class py`_
==================================== ==================================== ===================================

.. _Get Brokers With Asset Class cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetBrokersWithAssetClass.cpp

.. _Get Brokers With Asset Class cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetBrokersWithAssetClass.cs

.. _Get Brokers With Asset Class java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetBrokersWithAssetClass.java

.. _Get Brokers With Asset Class py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetBrokersWithAssetClass.py

.. _Get Brokers With Asset Class vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetBrokersWithAssetClass.cls


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
                
                request = service.createRequest("GetBrokersWithAssetClass")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                request.set("EMSX_ASSET_CLASS","EQTY")  # one of EQTY, OPT, FUT or MULTILEG_OPT
                    
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                

Output:-

.. code-block:: none

    C:\Users\_scripts>py -3 GetBrokersWithAssetClass.py
    Bloomberg - EMSX API Example - GetBrokersWithAssetClass
    Connecting to localhost:8194
    Processing SESSION_STATUS event
    SessionConnectionUp = {
        server = "localhost:8194"
        encryptionStatus = "Clear"
    }

    Processing SESSION_STATUS event
    Session started...
    Processing SERVICE_STATUS event
    Service opened...
    Request: GetBrokersWithAssetClass = {
        EMSX_ASSET_CLASS = EQTY
    }

    Processing RESPONSE event
    MESSAGE: GetBrokersWithAssetClass = {
        EMSX_BROKERS[] = {
            "API", "BB", "BEXE", "BMTB", "EEUE", "EFIX", "RFQ", "TKOR"
        }
    }

    CORRELATION ID: 3
    MESSAGE TYPE: GetBrokersWithAssetClass
    EMSX_BROKER: API
    EMSX_BROKER: BB
    EMSX_BROKER: BEXE
    EMSX_BROKER: BMTB
    EMSX_BROKER: EEUE
    EMSX_BROKER: EFIX
    EMSX_BROKER: RFQ
    EMSX_BROKER: TKOR
    Processing SESSION_STATUS event
    SessionConnectionDown = {
        server = "localhost:8194"
    }

    Processing SESSION_STATUS event
    SessionTerminated = {
    }


Get Field Metadata Request
===========================
The ``GetFieldMetaData`` request provides all field metadata in a response message.


Full code sample:-

=========================== ========================== ==========================
`Get Field Meta Data cpp`_  `Get Field Meta Data cs`_  `Get Field Meta Data vba`_	
--------------------------- -------------------------- --------------------------
`Get Field Meta Data java`_ `Get Field Meta Data py`_
=========================== ========================== ==========================

.. _Get Field Meta Data cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetFieldMetaData.cpp

.. _Get Field Meta Data cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetFieldMetaData.cs

.. _Get Field Meta Data java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetFieldMetaData.java

.. _Get Field Meta Data py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetFieldMetaData.py

.. _Get Field Meta Data vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetFieldMetaData.cls


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
    
                request = service.createRequest("GetFieldMetaData")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                request.getElement("EMSX_FIELD_NAMES").appendValue("EMSX_TICKER")
                request.getElement("EMSX_FIELD_NAMES").appendValue("EMSX_P_A")

                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        


Get Teams Request
=================
The ``GetTeams`` request provides all the team details in a response message.


Full code sample:-

================= ================= =================
`Get Teams cpp`_  `Get Teams cs`_ 	`Get Teams vba`_
----------------- ----------------- -----------------
`Get Teams java`_ `Get Teams py`_
================= ================= =================

.. _Get Teams cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GetTeams.cpp

.. _Get Teams cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetTeams.cs

.. _Get Teams java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GetTeams.java

.. _Get Teams py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetTeams.py

.. _Get Teams vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GetTeams.cls


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
    
                request = service.createRequest("GetTeams")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        
	    

Get Trade Desks Request
=======================
The ``GetTradeDesks`` is AIM specific request and provides all the trade desk details in a response message.


Full code sample:-

===================== =================
`Get Trade Desks cs`_ 	
--------------------- -----------------
`Get Trade Desks py`_
===================== =================


.. _Get Trade Desks cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetTradeDesks.cs

.. _Get Trade Desks py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetTradeDesks.py


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
    
                request = service.createRequest("GetTradeDesks")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"   


Get Traders Request
===================

The ``GetTraders`` is AIM specific request and provides all the traders details in a response message.


Full code sample:-

================= =================
`Get Traders cs`_ 	
----------------- -----------------
`Get Traders py`_
================= =================


.. _Get Traders cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GetTraders.cs

.. _Get Traders py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GetTraders.py



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
    
                request = service.createRequest("GetTraders")

                #request.set("EMSX_REQUEST_SEQ", 1)
                
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"   


Group Route Extended Request
============================
The ``GroupRouteEx`` request submits an entire list as a single route to a basket/program broker strategy destination.

This request should only be used if the intention is to submit an entire list or basket of securities to a single broker strategy destination. This should not be confused with maintaining a list or a basket from a portfolio perspective.

Currently, this is a three-step process in EMSX API.  

The first step is for the user will need to use ``CreateOrder`` request to create the order. Once the orders are created, the user will use ``CreateBasket`` request to create the basket or list of orders and use ``EMSX_BASKET_NAME`` element to specify the basket name. 

The next step is to submit the list using ``GroupRouteEx`` request and include the ``EMSX_SEQUENCE`` number inside the array. 


.. important::

    Please remember that the application does need to wait for confirmation of the basket creation to trigger the the ``GroupRouteEx`` request. The ``GroupRouteEx`` request is NOT independent of the basket creation for routing (placements).


Full code sample:-

============================ =========================== ===========================
`Group Route Extended cpp`_  `Group Route Extended cs`_  `Group Route Extended vba`_	
---------------------------- --------------------------- ---------------------------
`Group Route Extended java`_ `Group Route Extended py`_
============================ =========================== ===========================

.. _Group Route Extended cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/GroupRouteEx.cpp

.. _Group Route Extended cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/GroupRouteEx.cs

.. _Group Route Extended java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/GroupRouteEx.java

.. _Group Route Extended py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/GroupRouteEx.py

.. _Group Route Extended vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/GroupRoute.cls


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
	    
	                request = service.createRequest("GroupRouteEx")

	                # Multiple order numbers can be added
	                request.append("EMSX_SEQUENCE", 3745211) 
	                request.append("EMSX_SEQUENCE", 3745212) 
	                request.append("EMSX_SEQUENCE", 3745213) 

	                # The fields below are mandatory
	                request.set("EMSX_AMOUNT_PERCENT", 100)  # Note the amount here is %age of order amount
	                request.set("EMSX_BROKER", "BMTB");
	                
	                # For GroupRoute, the below values need to be added, but are taken 
	                # from the original order when the route is created.
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_TIF", "DAY")
	            
	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                #request.set("EMSX_BOOKNAME","BookName")
	                #request.set("EMSX_CFD_FLAG", "1")
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
	                #request.set("EMSX_RELEASE_TIME", 1259)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_STOP_PRICE", 123.5)
	                #request.set("EMSX_TRADER_UUID", 1234567)
	                
	                # Set the Request Type if this is for multi-leg orders
	                # only valid for options
	                '''
	                requestType = request.getElement("EMSX_REQUEST_TYPE") 
	                requestType.setChoice("Multileg")
	                multileg = requestType.getElement("Multileg")
	                multileg.setElement("EMSX_AMOUNT",10)
	                multileg.getElement("EMSX_ML_RATIO").appendValue(2)
	                multileg.getElement("EMSX_ML_RATIO").appendValue(3)
	                '''
	                
	                # Add the Route Ref ID values
	                routeRefIDPairs = request.getElement("EMSX_ROUTE_REF_ID_PAIRS")
	                route1 = routeRefIDPairs.appendElement()
	                route1.setElement("EMSX_ROUTE_REF_ID","MyRouteRef1")
	                route1.setElement("EMSX_SEQUENCE",3745211)
	                
	                route2 = routeRefIDPairs.appendElement();
	                route2.setElement("EMSX_ROUTE_REF_ID","MyRouteRef2")
	                route2.setElement("EMSX_SEQUENCE",3745212)
	                
	                route3 = routeRefIDPairs.appendElement()
	                route3.setElement("EMSX_ROUTE_REF_ID","MyRouteRef3")
	                route3.setElement("EMSX_SEQUENCE",3745213)
	                
	                # Below we establish the strategy details. Strategy details
	                # are common across all orders in a GroupRoute operation.
	                
	                strategy = request.getElement("EMSX_STRATEGY_PARAMS")
	                strategy.setElement("EMSX_STRATEGY_NAME", "VWAP")
	                
	                indicator = strategy.getElement("EMSX_STRATEGY_FIELD_INDICATORS")
	                data = strategy.getElement("EMSX_STRATEGY_FIELDS")
	                
	                # Strategy parameters must be appended in the correct order. See the output 
	                # of GetBrokerStrategyInfo request for the order. The indicator value is 0 for 
	                # a field that carries a value, and 1 where the field should be ignored
	                
	                data.appendElement().setElement("EMSX_FIELD_DATA", "09:30:00")  # StartTime
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "10:30:00")  # EndTime
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # Max%Volume
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # %AMSession
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # OPG
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # MOC
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # CompletePX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
	                   
	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # TriggerPX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # DarkComplete
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # DarkCompPX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # RefIndex
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")          # Discretion
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        


Group Route Extended Request - Multi-Leg Options
================================================
The multi-leg options can be traded using ``GroupRouteEx`` request. The first step is to create the 
options and if need be equities leg using ``CreateOrder`` request. Once this is completed, create a 
request object for ``GroupRouteEx`` and submit it to the session with all the fields necessary for the 
multi-leg options routing.

The overall workflow for multi-leg options is similar to how you create and submit a basket or a list in 
EMSX.

The ``CreateOrder`` request will essentially stage the multi-leg options orders into EMSX.  
(e.g. B/O on AAPL US 11/20/15 C121 Equity and B/O on AAPL US 11/20/15 P119 Equity. )

The multi-leg request is an array and similar to submitting a basket order, it is important to make sure 
the ``EMSX_SEQUENCE`` matches in the ``GroupRouteEx`` with the orders created using ``CreateOrder`` 
request. For the subscription services, there will initially be eight elements to subscribe at the Route 
level subscription.  They are ``EMSX_ML_ID``, ``EMSX_ML_LEG_QUANTITY``, ``EMSX_ML_NUM_LEGS``, ``EMSX_ML_PERCENT_FILLED``, ``EMSX_ML_RATIO``, ``EMSX_ML_REMAIN_BALANCE``, ``EMSX_ML_STRATEGY``, and ``EMSX_ML_TOTAL_QUANTITY``.

Please set the ``EMSX_REQEST_TYPE`` as ``Multileg`` to submit the multi-leg options using GroupRouteEx request.

.. note::

    The Debit and Credit is indicated by the  net price. Credit is indicated by using the negative sign 
    in the net price where the Debit is indicated by the positive net price. 
    
    The net price can be specified using the ``EMSX_LIMIT_PRICE`` element for the multi-leg options orders.

    ``Debit`` = positive for the net price
    
    ``Credit`` = negative for the net price


Group Route Extended Request - Route As Spread
==============================================
As of 15th of May, 2017 there also will be an ability to use GroupRouteEx to route two non-ticker as spread ticker in 
EMSX. 

The underlying concept remains the same and the only difference is to use ``EMSX_REQUEST_TYPE`` as a ``spread`` instead 
of ``Multileg`` and for ``EMSX_TICKER`` use one of the two tickers that makes the spread ticker. The ``EMSX_SEQUENCE`` 
inside the array to submit the list remains the same for using ``GroupRouteEx`` to route as a spread.


.. note::

    The ``EMSX_AMOUNT_PERCENT`` element for this request is used strictly for the amount in shares. 

    e.g. ``EMSX_AMOUNT_PERCENT``, 100 means it'll send 100 shares from each ticker.



Full code sample:-

`Route As Spread py`_   


.. _Route As Spread py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/RouteAsSpread.py


.. hint:: 

    Please right click on the top code sample link to open in a new tab.


.. code-block:: python
   :linenos:

    def routeSpread(self, session):
        
        request = self.service.createRequest("GroupRouteEx")

        request.append("EMSX_SEQUENCE", self.buySeqNo) 
        request.append("EMSX_SEQUENCE", self.sellSeqNo) 
        request.set("EMSX_AMOUNT_PERCENT", 100)
        request.set("EMSX_BROKER", "ETI");
        request.set("EMSX_HAND_INSTRUCTION", "ANY")
        request.set("EMSX_ORDER_TYPE", "MKT")
        request.set("EMSX_TIF", "DAY")
        request.set("EMSX_TICKER","CLN7 Comdty")
        request.set("EMSX_RELEASE_TIME",-1)
        requestType = request.getElement("EMSX_REQUEST_TYPE") 
        requestType.setChoice("Spread")
    
        print "Request: %s" % request.toString()
            
        self.requestID = blpapi.CorrelationId()
        
        session.sendRequest(request, correlationId=self.requestID )


Manaul Fill Request
===================
The ``ManualFill`` request can be used on the sell-side EMSX<GO> settings to create fills and notifies 
EMSX<GO>.


Full code sample:-

==================== =================== ================== 
`Manual Fill cpp`_   `Manual Fill cs`_   `Manual Fill vba`_
-------------------- ------------------- ------------------
`Manual Fill java`_  `Manual Fill py`_
==================== =================== ==================

.. _Manual Fill cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ManualFill.cpp

.. _Manual Fill cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/ManualFill.cs

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



Modify Order Extended Request
=============================
The ``ModifyOrderEx`` request modifies an existing or previously created order in EMSX<GO> or using EMSX API. 

.. important::

    Please note, when modifying an order or route, the limit price can be positive or negative. (e.g. Futures spreads). 
    There are two special cases for setting the limit price to 0. In the ``EMSX_LIMIT_PRICE`` a value of 0 means to ignore the value. 
    A value of ``EMSX_LIMIT_PRICE`` = -99999 means to reset the ``EMSX_LIMIT_PRICE`` to 0.


Full code sample:-

============================= ============================ ============================
`Modify Order Extended cpp`_  `Modify Order Extended cs`_  `Modify Order Extended vba`_ 	
----------------------------- ---------------------------- ----------------------------
`Modify Order Extended java`_ `Modify Order Extended py`_
============================= ============================ ============================

.. _Modify Order Extended cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ModifyOrderEx.cpp

.. _Modify Order Extended cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/ModifyOrderEx.cs

.. _Modify Order Extended java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/ModifyOrderEx.java

.. _Modify Order Extended py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/ModifyOrderEx.py

.. _Modify Order Extended vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/ModifyOrderEx.cls


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
    
                request = service.createRequest("ModifyOrderEx")

                # The fields below are mandatory
                request.set("EMSX_SEQUENCE", 3834157)
                request.set("EMSX_AMOUNT", 1300)
                request.set("EMSX_ORDER_TYPE", "MKT")
                request.set("EMSX_TIF", "DAY")
                request.set("EMSX_TICKER", "IBM US Equity")
            
                # The fields below are optional
                #request.set("EMSX_HAND_INSTRUCTION", "ANY")
                #request.set("EMSX_ACCOUNT","TestAccount")
                #request.set("EMSX_CFD_FLAG", "1")
                #request.set("EMSX_EXEC_INSTRUCTIONS", "AnyInst")
                #request.set("EMSX_GET_WARNINGS", "0")
                #request.set("EMSX_GTD_DATE", "20170105")
                #request.set("EMSX_INVESTOR_ID", "InvID")
                #request.set("EMSX_LIMIT_PRICE", 123.45)
                #request.set("EMSX_NOTES", "Some notes")
                #request.set("EMSX_REQUEST_SEQ", 1001)
                #request.set("EMSX_STOP_PRICE", 123.5)

                # Note: When changing order type to a LMT order, you will need to provide the EMSX_LIMIT_PRICE value.
                #       When changing order type away from LMT order, you will need to reset the EMSX_LIMIT_PRICE value
                #       by setting the content to -99999
                
                # Note: To clear down the stop price, set the content to -1
                
                # If modifying on behalf of another trader, set the order owner's UUID
                #request.set("EMSX_TRADER_UUID", 1234567)
                            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"        



Modify Route Extended Request
=============================
The ``ModifyRouteEx`` request modifies an existing or previously created child routes in EMSX<GO> or using EMSX API. 

.. important::

    Please note, when modifying an order or route, the limit price can be positive or negative. (e.g. Futures spreads). 
    There are two special cases for setting the limit price to 0. In the ``EMSX_LIMIT_PRICE`` a value of 0 means to ignore the value. 
    A value of ``EMSX_LIMIT_PRICE`` = -99999 means to reset the ``EMSX_LIMIT_PRICE`` to 0.


Full code sample:-

============================= ============================ ============================
`Modify Route Extended cpp`_  `Modify Route Extended cs`_  `Modify Route Extended vba`_    	
----------------------------- ---------------------------- ----------------------------
`Modify Route Extended java`_ `Modify Route Extended py`_ 
============================= ============================ ============================

.. _Modify Route Extended cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/ModifyRouteEx.cpp

.. _Modify Route Extended cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/ModifyRouteEx.cs

.. _Modify Route Extended java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/ModifyRouteEx.java

.. _Modify Route Extended py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/ModifyRouteEx.py

.. _Modify Route Extended vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/ModifyRouteEx.cls


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
    
                request = service.createRequest("ModifyRouteEx")

                # The fields below are mandatory
                request.set("EMSX_SEQUENCE", 3834157)
                request.set("EMSX_ROUTE_ID", 1)
                request.set("EMSX_AMOUNT", 1000)
                request.set("EMSX_ORDER_TYPE", "MKT")
                request.set("EMSX_TIF", "DAY")
            
                # The fields below are optional
                #request.set("EMSX_ACCOUNT","TestAccount")
                #request.set("EMSX_CLEARING_ACCOUNT", "ClearingAcnt")
                #request.set("EMSX_CLEARING_FIRM", "ClearingFirm")
                #request.set("EMSX_COMM_TYPE", "Absolute")
                #request.set("EMSX_EXCHANGE_DESTINATION", "DEST")
                #request.set("EMSX_GET_WARNINGS", "0")
                #request.set("EMSX_GTD_DATE", "20170105")
                #request.set("EMSX_LIMIT_PRICE", 123.45)
                #request.set("EMSX_LOC_BROKER", "ABCD")
                #request.set("EMSX_LOC_ID", "1234567")
                #request.set("EMSX_LOC_REQ", "Y")
                #request.set("EMSX_NOTES", "Some notes")
                #request.set("EMSX_ODD_LOT", "" )
                #request.set("EMSX_P_A", "P")
                #request.set("EMSX_REQUEST_SEQ", 1001)
                #request.set("EMSX_STOP_PRICE", 123.5)
                #request.set("EMSX_TRADER_NOTES", "Trader notes")
                #request.set("EMSX_USER_COMM_RATE", 0.02)
                #request.set("EMSX_USER_FEES", "1.5")

                # Note: When changing order type to a LMT order, you will need to provide the EMSX_LIMIT_PRICE value.
                #       When changing order type away from LMT order, you will need to reset the EMSX_LIMIT_PRICE value
                #       by setting the content to -99999
                
                # Note: To clear down the stop price, set the content to -1
                
                # Set the strategy parameters, if required
                
                '''
                strategy = request.getElement("EMSX_STRATEGY_PARAMS")
                strategy.setElement("EMSX_STRATEGY_NAME", "VWAP")
                
                indicator = strategy.getElement("EMSX_STRATEGY_FIELD_INDICATORS")
                data = strategy.getElement("EMSX_STRATEGY_FIELDS")
                
                # Strategy parameters must be appended in the correct order. See the output 
                # of GetBrokerStrategyInfo request for the order. The indicator value is 0 for 
                # a field that carries a value, and 1 where the field should be ignored
                
                data.appendElement().setElement("EMSX_FIELD_DATA", "09:30:00") # StartTime
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

                data.appendElement().setElement("EMSX_FIELD_DATA", "10:30:00") # EndTime
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # Max%Volume
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # %AMSession
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # OPG
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # MOC
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # CompletePX
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
                   
                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # TriggerPX
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # DarkComplete
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # DarkCompPX
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # RefIndex
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

                data.appendElement().setElement("EMSX_FIELD_DATA", "")         # Discretion
                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
                '''
                
                # If modifying on behalf of another trader, set the order owner's UUID
                #request.set("EMSX_TRADER_UUID", 1234567)
                
                # If modifying a multi-leg route, indicate the Multileg ID 
                #request.getElement("EMSX_REQUEST_TYPE").setChoice("Multileg").setElement("EMSX_ML_ID", "123456")
                            
                print "Request: %s" % request.toString()
                    
                self.requestID = blpapi.CorrelationId()
                
                session.sendRequest(request, correlationId=self.requestID )
                            
            elif msg.messageType() == SERVICE_OPEN_FAILURE:
                print >> sys.stderr, "Error: Service failed to open"  




Route Extended Request
======================
The ``RouteEx`` request submits an existing order into various execution veneues. This request is used primarily to submit a child route based on previously created parent order. 


Full code sample:-

======================= ==================== =====================
`Route Extended cpp`_   `Route Extended cs`_ `Route Extended vba`_	
----------------------- -------------------- ---------------------
`Route Extended java`_  `Route Extended py`_
======================= ==================== =====================

.. _Route Extended cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/RouteEx.cpp

.. _Route Extended cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/RouteEx.cs

.. _Route Extended java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/RouteEx.java

.. _Route Extended py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/RouteEx.py

.. _Route Extended vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/RouteEx.cls


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
	                #request.set("EMSX_RELEASE_TIME", 1259)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_ROUTE_REF_ID", "UniqueRef")
	                #request.set("EMSX_STOP_PRICE", 123.5)
	                #request.set("EMSX_TRADER_UUID", 1234567)

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        


Route Manually Extended Request
===============================
The ``RouteManuallyEx`` requestis generally used for phone orders where the placement is external to EMSX API. This request creates an order and notifies EMSX<GO> that this order is routed to the execution venue.


Full code sample:-

======================= ==================== =====================
`Route Manually cpp`_   `Route Manually cs`_ `Route Manually vba`_	
----------------------- -------------------- ---------------------
`Route Manually java`_  `Route Manually py`_
======================= ==================== =====================

.. _Route Manually cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/RouteManually.cpp

.. _Route Manually cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/RouteManually.cs

.. _Route Manually java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/RouteManually.java

.. _Route Manually py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/RouteManually.py

.. _Route Manually vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/RouteManually.cls


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
	    
	                request = service.createRequest("RouteManuallyEx")

	                # The fields below are mandatory
	                request.set("EMSX_SEQUENCE", 3745218)  # Order number
	                request.set("EMSX_AMOUNT", 500)
	                request.set("EMSX_BROKER", "BB")
	                request.set("EMSX_HAND_INSTRUCTION", "ANY")
	                request.set("EMSX_ORDER_TYPE", "MKT")
	                request.set("EMSX_TICKER", "IBM US Equity")
	                request.set("EMSX_TIF", "DAY")
	            
	                # The fields below are optional
	                #request.set("EMSX_ACCOUNT","TestAccount")
	                #request.set("EMSX_BOOKNAME","BookName")
	                #request.set("EMSX_CFD_FLAG", "1")
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
	                #request.set("EMSX_RELEASE_TIME", 1259)
	                #request.set("EMSX_REQUEST_SEQ", 1001)
	                #request.set("EMSX_ROUTE_REF_ID", "UniqueRef")
	                #request.set("EMSX_STOP_PRICE", 123.5)
	                #request.set("EMSX_TRADER_UUID", 1234567)
	                
	                # Below we establish the strategy details
	                '''
	                strategy = request.getElement("EMSX_STRATEGY_PARAMS")
	                strategy.setElement("EMSX_STRATEGY_NAME", "VWAP")
	                
	                indicator = strategy.getElement("EMSX_STRATEGY_FIELD_INDICATORS")
	                data = strategy.getElement("EMSX_STRATEGY_FIELDS")
	                
	                # Strategy parameters must be appended in the correct order. See the output 
	                # of GetBrokerStrategyInfo request for the order. The indicator value is 0 for 
	                # a field that carries a value, and 1 where the field should be ignored
	                
	                data.appendElement().setElement("EMSX_FIELD_DATA", "09:30:00")  # StartTime
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "10:30:00")   # EndTime
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 0)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # Max%Volume
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
	                   
	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # %AMSession
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # OPG
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # MOC
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # CompletePX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
	                   
	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # TriggerPX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # DarkComplete
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # DarkCompPX
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # RefIndex
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)

	                data.appendElement().setElement("EMSX_FIELD_DATA", "")           # Discretion
	                indicator.appendElement().setElement("EMSX_FIELD_INDICATOR", 1)
	                '''

	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        


