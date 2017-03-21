EMSX History Request
====================


EMSX History service provides individual fill information via request/response service. The service name is 
``//blp/emsx.history`` for production and ``//blp/emsx.history.uat`` for test environment. 


.. note::

	Please note this service will not be available as part of ``//blp/emapisvc`` or ``//blp/emapisvc_beta`` service.


Unlike the //blp/emapisvc and //blp/emapisvc_beta service, //blp/emsx.history and //blp/emsx.history.uat service uses semi-camel character for the element names.


=================================== =================================================================
Element             				Description
=================================== =================================================================
Account								Trading account used in EMSX<GO>
Amount 								Total quantity of the order
AssetClass 							Asset class of the order
BasketId							ID of the basket
BasketName 							Name of the basket
BBGID								BBGID field
BlockId 							Block ID
Broker 								Executing broker name
ClearingAccount 					Clearing account detail
ClearingFirm 						Clearing firm detail
ContractExpDate 					Contract expiration date
CorrectedFillId 					Corrected fill ID
Currency 							Currency
Cusip 								CUSIP
DateTimeOfFill 						Date and time of the fill
Exchange 							Exchange details
ExecPrevSeqNo 						Previous sequence number of execution
ExecType 							Execution type details
ExecutingBrokers 					Executing broker details
FillId 								ID of the fill
FillPrice 							Price of the fill
FillShare 							Number of share of the fill
InvestorID 							Investor ID detail
IsCfd 								CFD flag
Isin 								ISIN detail
IsLeg 								I
LastCapacity 						Last capacity field in EMSX<GO>
LastMarket 							Last market detail
LimitPrice 							Limit price detail
Liquidity 							Liquidity detail
LocalExchangeSymbol 				Local exchange symbol
LocateBroker 						Locate broker detail
LocateId 							Locate ID 
LocateRequired 						Flag to indicate whether or not short locate is required
MultilegId 							Multileg ID
OCCSymbol 							OCC symbol
OrderExecutionInstruction 			Order execution instruction detail
OrderHandlingInstruction 			Order handling instruction detail
OrderId 							Order ID
OrderInstruction 					Order instruction detail
OrderOrigin 						Order origin detail
OrderReferenceId 					Order reference ID detail
OriginatingTraderUuid 				UUID of the originating trader
ReroutedBroker 						Rerouted broker details
RouteCommissionAmount 				Commission amount of the route
RouteCommissionRate 				Commission rate of the route
RouteExecutionInstruction 			Route execution instruction
RouteHandlingInstruction 			Route handling instruction
RouteId 							Route ID
RouteNetMoney 						Route net money
RouteNotes 							Route instructions
RouteShares 						Route shares
SecurityName 						Security name detail
Sedol 								SEDOL
SettlementDate 						Settlement date detail
Side 								Side
StopPrice 							Stop Price 
StrategyType 						Strategy Type
Ticker 								Ticker
TIF 								Time in Force
TraderName 							Name of the trader
TraderUuid 							Bloomberg UUID of the trader
Type 								Order type
UserCommissionAmount 				User commission amount
UserCommissionRate 					User commission rate
UserFees 							User fee detail
UserNetMoney 						User net money detail
YellowKey 							Bloomberg yellow key field detail
=================================== =================================================================


==================== ===================
`EMSX History cpp`_  `EMSX History cs`_ 
==================== ===================
`EMSX History java`_ `EMSX History py`_
==================== ===================

.. _EMSX History cpp: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%2B%2B/EMSXHistory.cpp

.. _EMSX History cs: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_C%23/EMSXHistory.cs

.. _EMSX History java: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Java/EMSXHistory.java

.. _EMSX History py: https://github.com/tkim/emsx_api_repository/blob/master/EMSXFullSet_Python/EMSXHistory.py


.. code-block:: python

	# EMSXHistory.py

	import sys
	import blpapi


	SESSION_STARTED         = blpapi.Name("SessionStarted")
	SESSION_STARTUP_FAILURE = blpapi.Name("SessionStartupFailure")
	SERVICE_OPENED          = blpapi.Name("ServiceOpened")
	SERVICE_OPEN_FAILURE    = blpapi.Name("ServiceOpenFailure")
	ERROR_INFO              = blpapi.Name("ErrorInfo")
	GET_FILLS_RESPONSE      = blpapi.Name("GetFillsResponse")

	d_service="//blp/emsx.history.uat"
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
	    
	                request = service.createRequest("GetFills")

	                request.set("FromDateTime", "2017-02-08T00:00:00.000+00:00")
	                request.set("ToDateTime", "2017-02-11T23:59:00.000+00:00")

	                scope = request.getElement("Scope")
	                
	                #scope.setChoice("Team")
	                #scope.setChoice("TradingSystem")
	                scope.setChoice("Uuids")
	                
	                #scope.setElement("Team", "SEXEGROUP")
	                #scope.setElement("TradingSystem",false)
	                
	                scope.getElement("Uuids").appendValue(8049857)

	                '''
	                scope.getElement("Uuids").appendValue(14348220);
	                scope.getElement("Uuids").appendValue(8639067);
	                scope.getElement("Uuids").appendValue(4674574);
	                '''
	                
	                filter = request.getElement("FilterBy")
	                
	                #filter.setChoice("Basket")
	                #filter.setChoice("Multileg")
	                #filter.setChoice("OrdersAndRoutes")
	                
	                #filter.getElement("Basket").appendValue("TESTRJC")
	                #filter.getElement("Multileg").appendValue("mymlegId")
	                
	                #newOrder = filter.getElement("OrdersAndRoutes").appendElement()
	                #newOrder.setElement("OrderId",4292580)
	                #newOrder.setElement("RouteId",1)
	                
	                print "Request: %s" % request.toString()
	                    
	                self.requestID = blpapi.CorrelationId()
	                
	                session.sendRequest(request, correlationId=self.requestID )
	                            
	            elif msg.messageType() == SERVICE_OPEN_FAILURE:
	                print >> sys.stderr, "Error: Service failed to open"        
	                
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

	                    fills = msg.getElement("Fills")
	                    
	                    for fill in fills.values():

	                        account = fill.getElement("Account").getValueAsString()
	                        amount = fill.getElement("Amount").getValueAsFloat()
	                        assetClass = fill.getElement("AssetClass").getValueAsString()
	                        basketId = fill.getElement("BasketId").getValueAsInteger()
	                        bbgid = fill.getElement("BBGID").getValueAsString()
	                        blockId = fill.getElement("BlockId").getValueAsString()
	                        broker = fill.getElement("Broker").getValueAsString()
	                        clearingAccount = fill.getElement("ClearingAccount").getValueAsString()
	                        clearingFirm = fill.getElement("ClearingFirm").getValueAsString()
	                        contractExpDate = fill.getElement("ContractExpDate").getValueAsString()
	                        correctedFillId = fill.getElement("CorrectedFillId").getValueAsInteger()
	                        currency = fill.getElement("Currency").getValueAsString()
	                        cusip = fill.getElement("Cusip").getValueAsString()
	                        dateTimeOfFill = fill.getElement("DateTimeOfFill").getValueAsString()
	                        exchange = fill.getElement("Exchange").getValueAsString()
	                        execPrevSeqNo = fill.getElement("ExecPrevSeqNo").getValueAsInteger()
	                        execType = fill.getElement("ExecType").getValueAsString()
	                        executingBroker = fill.getElement("ExecutingBroker").getValueAsString()
	                        fillId = fill.getElement("FillId").getValueAsInteger()
	                        fillPrice = fill.getElement("FillPrice").getValueAsFloat()
	                        fillShares = fill.getElement("FillShares").getValueAsFloat()
	                        investorId = fill.getElement("InvestorID").getValueAsString()
	                        isCFD = fill.getElement("IsCfd").getValueAsBool()
	                        isin = fill.getElement("Isin").getValueAsString()
	                        isLeg = fill.getElement("IsLeg").getValueAsBool()
	                        lastCapacity = fill.getElement("LastCapacity").getValueAsString()
	                        lastMarket = fill.getElement("LastMarket").getValueAsString()
	                        limitPrice = fill.getElement("LimitPrice").getValueAsFloat()
	                        liquidity = fill.getElement("Liquidity").getValueAsString()
	                        localExchangeSymbol = fill.getElement("LocalExchangeSymbol").getValueAsString()
	                        locateBroker = fill.getElement("LocateBroker").getValueAsString()
	                        locateId = fill.getElement("LocateId").getValueAsString()
	                        locateRequired = fill.getElement("LocateRequired").getValueAsBool()
	                        multiLedId = fill.getElement("MultilegId").getValueAsString()
	                        occSymbol = fill.getElement("OCCSymbol").getValueAsString()
	                        orderExecutionInstruction = fill.getElement("OrderExecutionInstruction").getValueAsString()
	                        orderHandlingInstruction = fill.getElement("OrderHandlingInstruction").getValueAsString()
	                        orderId = fill.getElement("OrderId").getValueAsInteger()
	                        orderInstruction = fill.getElement("OrderInstruction").getValueAsString()
	                        orderOrigin = fill.getElement("OrderOrigin").getValueAsString()
	                        orderReferenceId = fill.getElement("OrderReferenceId").getValueAsString()
	                        originatingTraderUUId = fill.getElement("OriginatingTraderUuid").getValueAsInteger()
	                        reroutedBroker = fill.getElement("ReroutedBroker").getValueAsString()
	                        routeCommissionAmount = fill.getElement("RouteCommissionAmount").getValueAsFloat()
	                        routeCommissionRate = fill.getElement("RouteCommissionRate").getValueAsFloat()
	                        routeExecutionInstruction = fill.getElement("RouteExecutionInstruction").getValueAsString()
	                        routeHandlingInstruction = fill.getElement("RouteHandlingInstruction").getValueAsString()
	                        routeId = fill.getElement("RouteId").getValueAsInteger()
	                        routeNetMoney = fill.getElement("RouteNetMoney").getValueAsFloat()
	                        routeNotes = fill.getElement("RouteNotes").getValueAsString()
	                        routeShares = fill.getElement("RouteShares").getValueAsFloat()
	                        securityName = fill.getElement("SecurityName").getValueAsString()
	                        sedol = fill.getElement("Sedol").getValueAsString()
	                        settlementDate = fill.getElement("SettlementDate").getValueAsString()
	                        side = fill.getElement("Side").getValueAsString()
	                        stopPrice = fill.getElement("StopPrice").getValueAsFloat()
	                        strategyType = fill.getElement("StrategyType").getValueAsString()
	                        ticker = fill.getElement("Ticker").getValueAsString()
	                        tif = fill.getElement("TIF").getValueAsString()
	                        traderName = fill.getElement("TraderName").getValueAsString()
	                        traderUUId = fill.getElement("TraderUuid").getValueAsInteger()
	                        type = fill.getElement("Type").getValueAsString()
	                        userCommissionAmount = fill.getElement("UserCommissionAmount").getValueAsFloat()
	                        userCommissionRate = fill.getElement("UserCommissionRate").getValueAsFloat()
	                        userFees = fill.getElement("UserFees").getValueAsFloat()
	                        userNetMoney = fill.getElement("UserNetMoney").getValueAsFloat()
	                        yellowKey = fill.getElement("YellowKey").getValueAsString()
	                            
	                        print "OrderId: %d\tFill ID: %d\tDate/Time: %s\tShares: %f\tPrice: %f" % (orderId,fillId, dateTimeOfFill, fillShares, fillPrice)
	                            
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
	    print "Bloomberg - EMSX API Example - EMSXHistory"
	    try:
	        main()
	    except KeyboardInterrupt:
	        print  "Ctrl+C pressed. Stopping..."