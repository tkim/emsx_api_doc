EMSX History Request
====================
EMSX history service provides individual fill information via request/response service. The service name is 
``//blp/emsx.history`` for production and ``//blp/emsx.history.uat`` for test environment. 

This service should not be used as a replacement for route subscription service to capture fills information in real-time.  Anyone found to abuse the service by making constant calls to the history service will be shutdown permanently by Bloomberg.

A UUID's fills are only available if any of the following criteria are met:

1. The user has at least one Export Fill profile in ``EMSI<GO>``, or
2. The user belongs to a team that is setup for team fill export, or
3. The user is an EMSX API user and has EMSX API access turned on in ``EMSS<GO>`` setting.

Unlike the EMSX API service ``//blp/emapisvc`` and ``//blp/emapisvc_beta``, the history service supports ``PARTIAL_RESPONSE`` events.
The ``PARTIAL_RESPONSE`` event messages will return messages that are a subset of the information.

The EMSX history service goes back up to 30 days in history.

.. note::

	Please note this service will not be available as part of ``//blp/emapisvc`` or ``//blp/emapisvc_beta`` service.


Unlike the ``//blp/emapisvc`` and ``//blp/emapisvc_beta`` service, ``//blp/emsx.history`` and ``//blp/emsx.history.uat`` service uses semi-camel character for the element names.

.. important::
	
	Please note that the timezone of this service will always be in US EST timezone for the fills regardless of the TZDF setting for the UUID.

	Please note that EMSX History should never be used as a replacement for route subscription for real-time fills and updates to routes.


=================================== ===================================================================
Element             				Description
=================================== ===================================================================
``Account``							Trading account used in EMSX<GO>
``Amount`` 							Total quantity of the order
``AssetClass`` 						Asset class of the order
``BasketId``						ID of the basket
``BasketName`` 						Name of the basket
``BBGID``							BBGID field
``BlockId`` 						Block ID
``Broker`` 							Executing broker name
``BrokerExceId``                    Broker Execution ID
``BrokerOrderId``                   Broker Order ID
``ClearingAccount`` 				Clearing account detail
``ClearingFirm`` 					Clearing firm detail
``ContractExpDate`` 				Contract expiration date
``CorrectedFillId`` 				Corrected fill ID
``Currency`` 						Currency
``Cusip`` 							CUSIP
``DateTimeOfFill`` 					Date and time of the fill
``Exchange`` 						Exchange details
``ExecPrevSeqNo`` 					Previous sequence number of execution
``ExecType`` 						Execution type details (FILL,CANCEL,CORRECT and DFD)
``ExecutingBroker`` 				Executing broker details
``FillId``							ID of the fill
``FillPrice`` 						Price of the fill
``FillShares`` 						Number of share of the fill
``InvestorID`` 						Investor ID detail
``IsCfd`` 							CFD flag
``Isin`` 							ISIN detail
``IsLeg`` 							Is leg
``LastCapacity`` 					Last capacity field in EMSX<GO>
``LastMarket`` 						Last market detail
``LimitPrice`` 						Limit price detail
``Liquidity`` 						Last liquidity indicator 1,2,3,M,T,A [definition]_.
``LocalExchangeSymbol`` 			Local exchange symbol
``LocateBroker`` 					Locate broker detail
``LocateId`` 						Locate ID 
``LocateRequired`` 					Flag to indicate whether or not short locate is required
``MifidAggrFlag``                   Aggregation flag for MiFID II
``MifidBuysideLei``                 Legal entity identifier in MiFID II for the buy-side
``MifidGpi``                        Global personal identifier in MiFID II
``MifidIsSi``                       Flag to indiciate systematic internalizer in MiFID II
``MifidSellsideApaMic``             Sell-side Approved Publication Arrangment (APA) Market Identifier Code (MIC)
``MifidSellsideLei``                Legal entity identifier in MiFID II for the sell-side 
``MifidSellsideOtcFlag``            Sell-side OTC flag 
``MifidSellsideSiMic``              Sell-side systematic internalizer MIC
``MifidSellsideTri``                
``MifidSellsideTriMic``
``MifidSellsideWaiverFlag``         Sell-side waiver flag for MiFID II
``MifidTradeInstr``                 Trade instruction for MiFID II
``Mpid``
``MultilegId`` 						Multileg ID
``NyOrderCreateAsOfDateTime``       NY order create as of datetime 
``NyTranCreateAsOfDateTime``        NY transaction create as of datetime
``OCCSymbol`` 						OCC symbol
``OrderExecutionInstruction`` 		Order execution instruction detail
``OrderHandlingInstruction`` 		Order handling instruction detail
``OrderId`` 						Order ID
``OrderInstruction`` 				Order instruction detail
``OrderOrigin`` 					Order origin detail
``OrderReferenceId``				Order reference ID detail
``OriginatingTraderUuid`` 			UUID of the originating trader
``ReroutedBroker`` 					Rerouted broker details
``RouteCommissionAmount`` 			Commission amount of the route
``RouteCommissionRate`` 			Commission rate of the route
``RouteExecutionInstruction`` 		Route execution instruction
``RouteHandlingInstruction`` 		Route handling instruction
``RouteId`` 						Route ID
``RouteNetMoney`` 					Route net money
``RouteNotes`` 						Route instructions
``RouteShares`` 					Route shares
``SecurityName`` 					Security name detail
``Sedol`` 							SEDOL
``SettlementDate`` 					Settlement date detail
``Side`` 							Side
``StopPrice`` 						Stop Price 
``StrategyType``					Strategy Type
``Ticker`` 							Ticker
``TIF`` 							Time in Force
``TraderName`` 						Name of the trader
``TraderUuid`` 						Bloomberg UUID of the trader
``Type`` 							Order type
``UserCommissionAmount`` 			User commission amount
``UserCommissionRate`` 				User commission rate
``UserFees``						User fee detail
``UserNetMoney`` 					User net money detail
``YellowKey`` 						Bloomberg yellow key field detail
=================================== ===================================================================


Full code sample:-

==================== =================== ===================
`EMSX History cpp`_  `EMSX History cs`_  `EMSX History vba`_
-------------------- ------------------- -------------------
`EMSX History java`_ `EMSX History py`_
==================== =================== ===================

.. _EMSX History cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/EMSXHistory.cpp 

.. _EMSX History cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/EMSXHistory.cs

.. _EMSX History java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/EMSXHistory.java

.. _EMSX History py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/EMSXHistory.py

.. _EMSX History vba: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_VBA/EMSXHistory.cls

.. hint:: 

	Please right click on the top code sample link to open in a new tab.


Specify service name and host/port :-


.. code-block:: python
   

	d_service="//blp/emsx.history.uat"
	d_host="localhost"
	d_port=8194
	bEnd=False	


Connect and create a session object:-


.. code-block:: python


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


Set elements (e.g. UUID, team name, and Date/Time range):-

	               
.. code-block:: python
   	

	service = session.getService(d_service)

	request = service.createRequest("GetFills")

	request.set("FromDateTime", "2017-02-08T00:00:00.000+00:00")
	request.set("ToDateTime", "2017-02-11T23:59:00.000+00:00")

	scope = request.getElement("Scope")

	#scope.setChoice("Team")
	#scope.setChoice("TradingSystem")
	scope.setChoice("Uuids")

	#scope.setElement("Team", "TEAM1")
	#scope.setElement("TradingSystem",false)

	scope.getElement("Uuids").appendValue(8049857)

	'''
	scope.getElement("Uuids").appendValue(14348220);
	scope.getElement("Uuids").appendValue(8639067);
	scope.getElement("Uuids").appendValue(4674574);
	'''


Process response events:-


.. code-block:: python
   
		
    def processResponseEvent(self, event):
        print "Processing RESPONSE event"
        
        for msg in event:

            if msg.correlationIds()[0].value() == self.requestID.value():
                print "MESSAGE TYPE: %s" % msg.messageType()
                
                if msg.messageType() == ERROR_INFO:
                    errorCode = msg.getElementAsInteger("ERROR_CODE")
                    errorMessage = msg.getElementAsString("ERROR_MESSAGE")
                    print "ERROR CODE: %d\tERROR MESSAGE: %s" % (errorCode,errorMessage)
                elif msg.messageType() == GET_FILLS_RESPONSE:


Output:-

.. code-block:: none

	C:\Users\_scripts>py -3 EMSXHistory.py
	Bloomberg - EMSX API Example - EMSXHistory
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
	Request: GetFills = {
		FromDateTime = 2019-10-01T00:00:00.000+00:00
		ToDateTime = 2020-01-13T23:59:00.000+00:00
		Scope = {
			Uuids[] = {
				12345678
			}
		}
	}

	Processing RESPONSE event
	MESSAGE TYPE: GetFillsResponse
	Date: 2019-12-12T11:35:02.674-05:00
	Fill ID: 3      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 10112
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T11:50:02.717-05:00
	Fill ID: 4      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 20224
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T12:05:02.758-05:00
	Fill ID: 5      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 30336
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T12:20:02.799-05:00
	Fill ID: 6      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 40448
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T12:35:02.841-05:00
	Fill ID: 7      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 50560
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T12:50:02.881-05:00
	Fill ID: 8      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 60672
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2019-12-12T13:05:02.923-05:00
	Fill ID: 9      OrderId: 4733965        RouteId: 1
	Ticker: FB      Asset Class: Equity     Yellow Key: Equity
	Shares: 50      Price: 202.240000       Broker: BMTB    CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 70784
	Basket ID: 0    Currency: USD   Multileg ID:
	Account: testAccount    LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2020-01-13T14:01:23.880-05:00
	Fill ID: 11     OrderId: 4747927        RouteId: 2
	Ticker: MSFT    Asset Class: Equity     Yellow Key: Equity
	Shares: 20      Price: 161.330000       Broker: BB      CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 29039
	Basket ID: 0    Currency: USD   Multileg ID:
	Account:        LocateId:                       LocateBroker: False     OCCSymbol:
	Date: 2020-01-13T14:01:53.882-05:00
	Fill ID: 12     OrderId: 4747927        RouteId: 2
	Ticker: MSFT    Asset Class: Equity     Yellow Key: Equity
	Shares: 20      Price: 161.330000       Broker: BB      CFD: False
	Commission: 0   Commission Rate: 0      Fees: 0 Net Money: 32266
	Basket ID: 0    Currency: USD   Multileg ID:
	Account:        LocateId:                       LocateBroker: False     OCCSymbol:
	Processing SESSION_STATUS event
	SessionConnectionDown = {
		server = "localhost:8194"
	}

	Processing SESSION_STATUS event
	SessionTerminated = {
	}


.. [definition] 1=Added, 2=Removed, 3=Routed out, M=Maker, T=Taker, R=Rerouted, A=Auction


