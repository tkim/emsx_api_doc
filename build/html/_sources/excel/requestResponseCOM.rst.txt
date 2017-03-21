#######################################
Buy-Side Request/Response Service (COM)
#######################################


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


Description of Request/Response Service
=======================================


EMSX API supports the following Request/Response services:-

*Please note, the descriptions to the legacy request/response services are omitted from the description section.*

======================================= =================================================================
Request Name             			    Action
======================================= =================================================================
``AssignTrader``						Assign an order to another UUID.
``CancelRouteEx``						Cancel outstanding routes (placements).
``CreateOrder``                     	Create an order or stage an order into EMSX<GO>.
``CreateOrderAndRouteEx``				Create a new order and route in a single request. 
``CreateOrderAndRouteManually``	 		Create the order and notify EMSX this is routed.
``DeleteOrder``					 		Delete an existing order in EMSX<GO>.
``GetAllFieldMetaData``			 		Get all field meta data in a response message.
``GetBrokerStrategiesWithAssetClass`` 	Get all broker strategy information and asset class data.
``GetBrokerStrategyInfoWithAssetClass`` Get all broker strategy info and asset class data.
``GetBrokerWithAssetClass`` 			Get all broker data with asset class in a response message.
``GetFieldMetaData`` 					Get field meta data in a reponse message.
``GetTeams`` 							Get team data in a response message.
``GroupRouteEx`` 						Submit the entire list as a single route to a basket algorithm.
``ModifyOrder`` 						Modify parent order.
``ModifyRouteEx`` 						Modify child route.
``RouteEx`` 							Route existing order.
``RouteManuallyEx`` 					Route manually and notify EMSX that it is routed.
======================================= =================================================================



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


.. warning:: 

	The following ``EMSX_CUSTOM_NOTE*`` elements are not available on either order or route subscription service.


=================================== ==================================================================
Element								Description             		
=================================== ==================================================================
``EMSX_CUSTOM_NOTE1``				80-character free text field (79+1 check digit)
``EMSX_CUSTOM_NOTE2``				80-character free text field (79+1 check digit) 
``EMSX_CUSTOM_NOTE3``				80-character free text field (79+1 check digit) 
``EMSX_CUSTOM_NOTE4``				80-character free text field (79+1 check digit)
``EMSX_CUSTOM_NOTE5``				80-character free text field (79+1 check digit)
=================================== ==================================================================


Assign Trader Request (COM) 
===========================


``AssignTrader`` request allows EMSX API to reassign order to another user UUID. A typical setup will have the 
different UUID as another part of the TEAM setup for the order creater UUID. This will allow systematically generated 
trades to be reassigned to another human trader if need be from the EMSX API.

Assigned trader must be in same EMBR group for this to work. EMBR<GO> is an internal Bloomberg function the EMSX 
account managers will use to set this feature on behalf of the client. The EMSX account manager will check off the 
ability to reassign before the AssignTrader request will work. Once this feature is on, trading on behalf other UUID 
feature will no longer work for that team.


.. code-block:: vb.net

	Option Explicit

	Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
	Public running As Boolean
	Private svc As blpapicomLib2.service
	Private emsxService As String
	Private requestID As blpapicomLib2.CorrelationId

	Private Sub Class_Initialize()

	    log "Bloomberg - EMSX API Example - AssignTrader"

	    emsxService = "//blp/emapisvc_beta"
	    
	    Set m_BBG_EMSX = New blpapicomLib2.Session
	    
	    running = True
	    
	    m_BBG_EMSX.QueueEvents = True
	    m_BBG_EMSX.Start
	    

	End Sub

	Private Sub Class_Terminate()
	    Set m_BBG_EMSX = Nothing
	End Sub

	Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

	    On Error GoTo errHandler

	    Dim eventObj As blpapicomLib2.Event
	    
	     '   Assign the returned data to a Bloomberg type event
	    Set eventObj = obj
	    
	    If Application.Ready Then
	    
	        Select Case eventObj.EventType
	        
	            Case SESSION_STATUS
	                processSessionEvent eventObj
	                
	            Case BLPSERVICE_STATUS
	                processServiceEvent eventObj
	                
	            Case RESPONSE
	                processResponseEvent eventObj
	                
	        End Select
	        
	    End If

	    Exit Sub

	errHandler:
	    Dim errmsg As Variant
	    errmsg = Err.Description
	    log (errmsg)
	    running = False

	End Sub


	Private Sub processSessionEvent(evt As blpapicomLib2.Event)

	    log "Processing SESSION_STATUS event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "SessionStarted" Then
	            log "Session started..."
	            m_BBG_EMSX.OpenService emsxService
	        ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
	            log "Error: Session startup failed"
	            running = False
	        End If
	        
	    Loop

	End Sub

	Private Sub processServiceEvent(evt As blpapicomLib2.Event)

	    Dim req As REQUEST
	    Dim service As service
	    Dim it As blpapicomLib2.MessageIterator
	    
	    On Error GoTo failed
	    
	    log "Processing SERVICE_STATUS event"
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "ServiceOpened" Then
	    
	            ' Get the service
	            Set service = m_BBG_EMSX.GetService(emsxService)
	    
	            'First, create our request object
	            Set req = service.CreateRequest("AssignTrader")
	    
	            'req.Set "EMSX_REQUEST_SEQ", 1
	            
	            ' Append the order numbers...
	            req.Append "EMSX_SEQUENCE", 3741104
	            req.Append "EMSX_SEQUENCE", 3741105
	            
	            req.Set "EMSX_ASSIGNEE_TRADER_UUID", 12109783
	            
	            log "Request: " & req.Print
	            
	            ' Send the request
	            Set requestID = m_BBG_EMSX.SendRequest(req)

	        ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
	        
	            log "Error: Service failed to open"
	            running = False
	            
	        End If
	        
	    Loop

	    Exit Sub
	    
	failed:

	    log "Failed to send the request: " + Err.Description
	    
	    running = False
	    Exit Sub
	    
	End Sub

	Private Sub processResponseEvent(evt As blpapicomLib2.Event)

	    log "Processing RESPONSE event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    Dim i As Integer
	    Dim successful As Element
	    Dim failed As Element
	    Dim order As Element
	    Dim numValues As Integer
	    Dim errorCode As Long
	    Dim errorMessage As String
	    Dim success As Boolean

	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
	        
	            If msg.MessageTypeAsString = "ErrorInfo" Then
	            
	                errorCode = msg.GetElement("ERROR_CODE")
	                errorMessage = msg.GetElement("ERROR_MESSAGE")
	                
	                log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
	            
	                running = False
	                
	            ElseIf msg.MessageTypeAsString = "AssignTrader" Then
	                
	                success = msg.GetElement("EMSX_ALL_SUCCESS")
	                
	                If success Then
	                    
	                    log "All orders successfully assigned"
	                    
	                    Set successful = msg.GetElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS")
	                    
	                    numValues = successful.numValues
	                    
	                    If numValues > 0 Then log "Successful assignments:-"
	                    
	                    For i = 0 To numValues - 1
	                        
	                        Set order = successful.GetValueAsElement(i)
	                        
	                        log order.GetElement("EMSX_SEQUENCE")
	                        
	                    Next i
	                
	                Else
	                
	                    log "One or more failed assignments..."
	                    
	                    If msg.AsElement.HasElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS") Then
	                    
	                        Set successful = msg.GetElement("EMSX_ASSIGN_TRADER_SUCCESSFUL_ORDERS")
	                    
	                        numValues = successful.numValues
	                    
	                        If numValues > 0 Then log "Successful assignments:-"
	                    
	                        For i = 0 To numValues - 1
	                            
	                            Set order = successful.GetValueAsElement(i)
	                        
	                            log order.GetElement("EMSX_SEQUENCE")
	                        
	                        Next i
	                    
	                    End If
	                    
	                    If msg.AsElement.HasElement("EMSX_ASSIGN_TRADER_FAILED_ORDERS") Then
	                    
	                        Set successful = msg.GetElement("EMSX_ASSIGN_TRADER_FAILED_ORDERS")
	                    
	                        numValues = successful.numValues
	                    
	                        If numValues > 0 Then log "Failed assignments:-"
	                    
	                        For i = 0 To numValues - 1
	                        
	                            Set order = successful.GetValueAsElement(i)
	                        
	                            log order.GetElement("EMSX_SEQUENCE")
	                        
	                        Next i
	                    
	                    End If
	                End If
	                
	                m_BBG_EMSX.Stop
	                running = False
	            
	            End If
	        End If
	    Loop

	End Sub



Cancel Route Extended Request (COM)
===================================


In EMSX<GO> we have a notion of parent order and child routes. The ``CancelRoute`` request is to effectively send out 
a cancellation request to the execution venue of the current live route. Submission of ``CancelRoute`` does not 
automatically cancel the outstanding route. This action needs to be acknowledged and performed by the execution venue 
of the route.


.. code-block:: vb.net

	Option Explicit

	Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
	Public running As Boolean
	Private svc As blpapicomLib2.service
	Private emsxService As String
	Private requestID As blpapicomLib2.CorrelationId

	Private Sub Class_Initialize()

	    log "Bloomberg - EMSX API Example - CancelRoute"

	    emsxService = "//blp/emapisvc_beta"
	    
	    Set m_BBG_EMSX = New blpapicomLib2.Session
	    
	    running = True
	    
	    m_BBG_EMSX.QueueEvents = True
	    m_BBG_EMSX.Start
	    

	End Sub

	Private Sub Class_Terminate()
	    Set m_BBG_EMSX = Nothing
	End Sub

	Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

	    On Error GoTo errHandler

	    Dim eventObj As blpapicomLib2.Event
	    
	     '   Assign the returned data to a Bloomberg type event
	    Set eventObj = obj
	    
	    If Application.Ready Then
	    
	        Select Case eventObj.EventType
	        
	            Case SESSION_STATUS
	                processSessionEvent eventObj
	                
	            Case BLPSERVICE_STATUS
	                processServiceEvent eventObj
	                
	            Case RESPONSE
	                processResponseEvent eventObj
	                
	        End Select
	        
	    End If

	    Exit Sub

	errHandler:
	    Dim errmsg As Variant
	    errmsg = Err.Description
	    log (errmsg)
	    running = False

	End Sub


	Private Sub processSessionEvent(evt As blpapicomLib2.Event)

	    log "Processing SESSION_STATUS event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "SessionStarted" Then
	            log "Session started..."
	            m_BBG_EMSX.OpenService emsxService
	        ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
	            log "Error: Session startup failed"
	            running = False
	        End If
	        
	    Loop

	End Sub

	Private Sub processServiceEvent(evt As blpapicomLib2.Event)

	    Dim req As REQUEST
	    Dim service As service
	    Dim routes As Element
	    Dim Route As Element
	    
	    Dim it As blpapicomLib2.MessageIterator
	    
	    On Error GoTo failed
	    
	    log "Processing SERVICE_STATUS event"
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "ServiceOpened" Then
	    
	            ' Get the service
	            Set service = m_BBG_EMSX.GetService(emsxService)
	    
	            'First, create our request object
	            Set req = service.CreateRequest("CancelRoute")
	    
	            'req.Set "EMSX_REQUEST_SEQ", 1
	            'req.Set "EMSX_TRADER_UUID", 1234567
	            
	            Set routes = req.GetElement("ROUTES")   'Note, the case is important
	            Set Route = routes.AppendElment()
	            
	            Route.SetElement "EMSX_SEQUENCE", 3741104
	            Route.SetElement "EMSX_ROUTE_ID", 1
	           
	            log "Request: " & req.Print
	            
	            ' Send the request
	            Set requestID = m_BBG_EMSX.SendRequest(req)

	        ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
	        
	            log "Error: Service failed to open"
	            running = False
	            
	        End If
	        
	    Loop

	    Exit Sub
	    
	failed:

	    log "Failed to send the request: " + Err.Description
	    
	    running = False
	    Exit Sub
	    
	End Sub

	Private Sub processResponseEvent(evt As blpapicomLib2.Event)

	    log "Processing RESPONSE event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    Dim i As Integer
	    Dim errorCode As Long
	    Dim errorMessage As String
	 
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
	        
	            If msg.MessageTypeAsString = "ErrorInfo" Then
	            
	                errorCode = msg.GetElement("ERROR_CODE")
	                errorMessage = msg.GetElement("ERROR_MESSAGE")
	                
	                log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
	            
	                running = False
	                
	            ElseIf msg.MessageTypeAsString = "CancelRoute" Then
	                              
	                Dim stat As String
	                Dim msgdesc As String
	                
	                stat = msg.GetElement("STATUS")
	                msgdesc = msg.GetElement("MESSAGE")
	                
	                log "STATUS: " & stat & "    MESSAGE: " & msgdesc
	                
	                m_BBG_EMSX.Stop
	                running = False
	            
	            End If
	        End If
	    Loop

	End Sub



Create Order Request
====================


Creating an order requires the user to create a request from the service object of type ``CreateOrder`` and fill in 
the required fields before submitting the request. 

If the handling instruction is for DMA access or any other non-standard handling instructions, EMSX API will not 
allow users to stage the order from the EMSX API unless the broker enables the broker code for EMSX API.  This is 
also true for custom Time in Force fields. Any non-standard TIF will also be restricted from staging unless the 
broker enables the broker code for EMSX API.

.. note::

	If the handling instruction is for DMA access or any other non-standard handling instructions, EMSX API will not 
	allow users to stage the order from the EMSX API unless the broker enables the broker code for EMSX API.  This is 
	also true for custom time in force fields. Any non-standard TIF will also be restricted from staging unless the 
	broker enables the broker code for EMSX API.


.. code-block:: vb.net

	Option Explicit

	Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
	Public running As Boolean
	Private svc As blpapicomLib2.service
	Private emsxService As String
	Private requestID As blpapicomLib2.CorrelationId

	Private Sub Class_Initialize()

	    log "Bloomberg - EMSX API Example - CreateOrder"

	    emsxService = "//blp/emapisvc_beta"
	    
	    Set m_BBG_EMSX = New blpapicomLib2.Session
	    
	    running = True
	    
	    m_BBG_EMSX.QueueEvents = True
	    m_BBG_EMSX.Start
	    

	End Sub

	Private Sub Class_Terminate()
	    Set m_BBG_EMSX = Nothing
	End Sub

	Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

	    On Error GoTo errHandler
	    
	    Dim eventObj As blpapicomLib2.Event
	    
	     '   Assign the returned data to a Bloomberg type event
	    Set eventObj = obj
	    
	    If Application.Ready Then
	    
	        Select Case eventObj.EventType
	        
	            Case SESSION_STATUS
	                processSessionEvent eventObj
	                
	            Case BLPSERVICE_STATUS
	                processServiceEvent eventObj
	                
	            Case RESPONSE
	                processResponseEvent eventObj
	                
	        End Select
	        
	    End If

	    Exit Sub

	errHandler:
	    Dim errmsg As Variant
	    errmsg = Err.Description
	    log (errmsg)
	    running = False

	End Sub


	Private Sub processSessionEvent(evt As blpapicomLib2.Event)

	    log "Processing SESSION_STATUS event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "SessionStarted" Then
	            log "Session started..."
	            m_BBG_EMSX.OpenService emsxService
	        ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
	            log "Error: Session startup failed"
	            running = False
	        End If
	        
	    Loop

	End Sub

	Private Sub processServiceEvent(evt As blpapicomLib2.Event)

	    Dim req As REQUEST
	    Dim service As service
	    
	    Dim it As blpapicomLib2.MessageIterator
	    
	    On Error GoTo failed
	    
	    log "Processing SERVICE_STATUS event"
	    
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If msg.MessageTypeAsString = "ServiceOpened" Then
	    
	            ' Get the service
	            Set service = m_BBG_EMSX.GetService(emsxService)
	    
	            'First, create our request object
	            Set req = service.CreateRequest("CreateOrder")
	    
	            'The fields below are mandatory
	            req.Set "EMSX_TICKER", "IBM US Equity"
	            req.Set "EMSX_AMOUNT", 1000
	            req.Set "EMSX_ORDER_TYPE", "MKT"
	            req.Set "EMSX_TIF", "DAY"
	            req.Set "EMSX_HAND_INSTRUCTION", "ANY"
	            req.Set "EMSX_SIDE", "BUY"
	            
	            'The fields below are optional
	            'req.Set "EMSX_ACCOUNT", "TestAccount"
	            'req.Set "EMSX_BASKET_NAME", "HedgingBasket"
	            'req.Set "EMSX_BROKER", "BMTB"
	            'req.Set "EMSX_CFD_FLAG", "1"
	            'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
	            'req.Set "EMSX_CLEARING_FIRM", "FirmName"
	            'req.Set "EMSX_CUSTOM_NOTE1", "Note1"
	            'req.Set "EMSX_CUSTOM_NOTE2", "Note2"
	            'req.Set "EMSX_CUSTOM_NOTE3", "Note3"
	            'req.Set "EMSX_CUSTOM_NOTE4", "Note4"
	            'req.Set "EMSX_CUSTOM_NOTE5", "Note5"
	            'req.Set "EMSX_EXCHANGE_DESTINATION", "ExchDest"
	            'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
	            'req.Set "EMSX_GET_WARNINGS", "0"
	            'req.Set "EMSX_GTD_DATE", "20170105"
	            'req.Set "EMSX_INVESTOR_ID", "InvID"
	            'req.Set "EMSX_LIMIT_PRICE", 123.45
	            'req.Set "EMSX_LOCATE_BROKER", "BMTB"
	            'req.Set "EMSX_LOCATE_ID", "SomeID"
	            'req.Set "EMSX_LOCATE_REQ", "Y"
	            'req.Set "EMSX_NOTES", "Some notes"
	            'req.Set "EMSX_ODD_LOT", "0"
	            'req.Set "EMSX_ORDER_ORIGIN", ""
	            'req.Set "EMSX_ORDER_REF_ID", "UniqueID"
	            'req.Set "EMSX_P_A", "P"
	            'req.Set "EMSX_RELEASE_TIME", 34341
	            'req.Set "EMSX_REQUEST_SEQ", 1001
	            'req.Set "EMSX_SETTLE_CURRENCY", "USD"
	            'req.Set "EMSX_SETTLE_DATE", 20170106
	            'req.Set "EMSX_SETTLE_TYPE", "T+2"
	            'req.Set "EMSX_STOP_PRICE", 123.5
	           
	            log "Request: " & req.Print
	            
	            ' Send the request
	            Set requestID = m_BBG_EMSX.SendRequest(req)

	        ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
	        
	            log "Error: Service failed to open"
	            running = False
	            
	        End If
	        
	    Loop

	    Exit Sub
	    
	failed:

	    log "Failed to send the request: " + Err.Description
	    
	    running = False
	    Exit Sub
	    
	End Sub

	Private Sub processResponseEvent(evt As blpapicomLib2.Event)

	    log "Processing RESPONSE event"
	    
	    Dim it As blpapicomLib2.MessageIterator
	    Dim i As Integer
	    Dim errorCode As Long
	    Dim errorMessage As String
	 
	    Set it = evt.CreateMessageIterator()

	    ' Loop while we have messages remaining
	    Do While it.Next()
	              
	        Dim msg As Message
	        
	        '   Pick up message
	        Set msg = it.Message
	        
	        log "MessageType: " + msg.MessageTypeAsString
	        
	        If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
	        
	            If msg.MessageTypeAsString = "ErrorInfo" Then
	            
	                errorCode = msg.GetElement("ERROR_CODE")
	                errorMessage = msg.GetElement("ERROR_MESSAGE")
	                
	                log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
	            
	                running = False
	                
	            ElseIf msg.MessageTypeAsString = "CreateOrder" Then
	                
	                Dim emsxSequence As Long
	                Dim msgdesc As String
	                
	                emsxSequence = msg.GetElement("EMSX_SEQUENCE")
	                msgdesc = msg.GetElement("MESSAGE")
	                
	                log "EMSX_SEQUENCE: " & emsxSequence & "    MESSAGE: " & msgdesc
	                
	                m_BBG_EMSX.Stop
	                running = False
	            
	            End If
	        End If
	    Loop

	End Sub


Create Order And Route Extended Request (COM)
=============================================


Creating an order and routing with strategy requires the user to create a request from the service object of type ``CreateOrderAndRouteEx`` and fill in the required fields before submitting the request. 
Mandatory fields for the CreateOrderAndRoute requests are the following. 


.. note:: 

	The user will first need to request ``GetBrokers`` to get all the brokers the user is enabled for, returned in response. Subsequently the user can then request ``GetBrokerStrategies`` to get all the broker strategies user is enabled for that particular broker code. 

	Lastly, ``GetBrokerStrategyInfo`` will get all the fields for the provided broker strategy in the particular order in which they need to be submitted in ``CreateOrderAndRouteEx`` and ``RouteEx`` requests.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - CreateOrderAndRoute"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("CreateOrderAndRouteEx")
        
                'The fields below are mandatory
                req.Set "EMSX_TICKER", "IBM US Equity"
                req.Set "EMSX_AMOUNT", 1000
                req.Set "EMSX_ORDER_TYPE", "MKT"
                req.Set "EMSX_TIF", "DAY"
                req.Set "EMSX_HAND_INSTRUCTION", "ANY"
                req.Set "EMSX_SIDE", "BUY"
                req.Set "EMSX_BROKER", "BB"
                
                'The fields below are optional
                'req.Set "EMSX_ACCOUNT", "TestAccount"
                'req.Set "EMSX_BOOKNAME", "HedgingBasket"
                'req.Set "EMSX_BASKET_NAME", "HedgingBasket"
                'req.Set "EMSX_CFD_FLAG", "1"
                'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
                'req.Set "EMSX_CLEARING_FIRM", "FirmName"
                'req.Set "EMSX_CUSTOM_NOTE1", "Note1"
                'req.Set "EMSX_CUSTOM_NOTE2", "Note2"
                'req.Set "EMSX_CUSTOM_NOTE3", "Note3"
                'req.Set "EMSX_CUSTOM_NOTE4", "Note4"
                'req.Set "EMSX_CUSTOM_NOTE5", "Note5"
                'req.Set "EMSX_EXCHANGE_DESTINATION", "ExchDest"
                'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
                'req.Set "EMSX_GET_WARNINGS", "0"
                'req.Set "EMSX_GTD_DATE", "20170105"
                'req.Set "EMSX_INVESTOR_ID", "InvID"
                'req.Set "EMSX_LIMIT_PRICE", 123.45
                'req.Set "EMSX_LOCATE_BROKER", "BMTB"
                'req.Set "EMSX_LOCATE_ID", "SomeID"
                'req.Set "EMSX_LOCATE_REQ", "Y"
                'req.Set "EMSX_NOTES", "Some notes"
                'req.Set "EMSX_ODD_LOT", "0"
                'req.Set "EMSX_ORDER_ORIGIN", ""
                'req.Set "EMSX_ORDER_REF_ID", "UniqueID"
                'req.Set "EMSX_P_A", "P"
                'req.Set "EMSX_RELEASE_TIME", 34341
                'req.Set "EMSX_REQUEST_SEQ", 1001
                'req.Set "EMSX_ROUTE_REF_ID", "UniqueID"
                'req.Set "EMSX_SETTLE_CURRENCY", "USD"
                'req.Set "EMSX_SETTLE_DATE", 20170106
                'req.Set "EMSX_SETTLE_TYPE", "T+2"
                'req.Set "EMSX_STOP_PRICE", 123.5
               
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "CreateOrderAndRouteEx" Then
                    
                    Dim emsxSequence As Long
                    Dim emsxRouteId As Integer
                    Dim msgdesc As String
                    
                    emsxSequence = msg.GetElement("EMSX_SEQUENCE")
                    emsxRouteId = msg.GetElement("EMSX_ROUTE_ID")
                    msgdesc = msg.GetElement("MESSAGE")
                    
                    log "EMSX_SEQUENCE: " & emsxSequence & "    EMSX_ROUTE_ID: " & emsxRouteId & "    MESSAGE: " & msgdesc
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Create Order And Route Manually Extended Request (COM)
======================================================


``CreateOrderAndRouteManually`` request is generally used for phone orders where the placement is external to EMSX API. This request creates an order and notifies EMSX<GO> that this order is routed to the execution venue.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - CreateOrderAndRouteManually"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("CreateOrderAndRouteManually")
        
                'The fields below are mandatory
                req.Set "EMSX_TICKER", "IBM US Equity"
                req.Set "EMSX_AMOUNT", 1000
                req.Set "EMSX_ORDER_TYPE", "MKT"
                req.Set "EMSX_TIF", "DAY"
                req.Set "EMSX_HAND_INSTRUCTION", "ANY"
                req.Set "EMSX_SIDE", "BUY"
                req.Set "EMSX_BROKER", "BB"
                
                'The fields below are optional
                'req.Set "EMSX_ACCOUNT", "TestAccount"
                'req.Set "EMSX_CFD_FLAG", "1"
                'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
                'req.Set "EMSX_CLEARING_FIRM", "FirmName"
                'req.Set "EMSX_EXCHANGE_DESTINATION", "ExchDest"
                'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
                'req.Set "EMSX_GET_WARNINGS", "0"
                'req.Set "EMSX_GTD_DATE", "20170105"
                'req.Set "EMSX_INVESTOR_ID", "InvID"
                'req.Set "EMSX_LIMIT_PRICE", 123.45
                'req.Set "EMSX_LOCATE_BROKER", "BMTB"
                'req.Set "EMSX_LOCATE_ID", "SomeID"
                'req.Set "EMSX_LOCATE_REQ", "Y"
                'req.Set "EMSX_NOTES", "Some notes"
                'req.Set "EMSX_ODD_LOT", "0"
                'req.Set "EMSX_ORDER_ORIGIN", ""
                'req.Set "EMSX_ORDER_REF_ID", "UniqueID"
                'req.Set "EMSX_P_A", "P"
                'req.Set "EMSX_RELEASE_TIME", 34341
                'req.Set "EMSX_REQUEST_SEQ", 1001
                'req.Set "EMSX_SETTLE_DATE", 20170106
                'req.Set "EMSX_STOP_PRICE", 123.5
               
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "CreateOrderAndRouteManually" Then
                    
                    Dim emsxSequence As Long
                    Dim emsxRouteId As Integer
                    Dim msgdesc As String
                    
                    emsxSequence = msg.GetElement("EMSX_SEQUENCE")
                    emsxRouteId = msg.GetElement("EMSX_ROUTE_ID")
                    msgdesc = msg.GetElement("MESSAGE")
                    
                    log "EMSX_SEQUENCE: " & emsxSequence & "    EMSX_ROUTE_ID: " & emsxRouteId & "    MESSAGE: " & msgdesc
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub


Delete Order Request (COM)
==========================


``DeleteOrder`` request deletes an existing order in EMSX<GO>. This is not the same action as canceling the parent order. In fact, EMSX API does not expose Cancel Order status as in EMSX<GO>. 

The primary reason behind this is because the Cancel Order in EMSX<GO> really just puts an order in an inoperable state and doesn't really serve any meaningful function.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - DeleteOrder"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("DeleteOrder")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                req.GetElement("EMSX_SEQUENCE").AppendValue 3741181
                req.GetElement("EMSX_SEQUENCE").AppendValue 3741182
               
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "DeleteOrder" Then
                    
                    Dim stat As Long
                    Dim msgdesc As String
                    
                    stat = msg.GetElement("STATUS")
                    msgdesc = msg.GetElement("MESSAGE")
                    
                    log "STATUS: " & stat & "    MESSAGE: " & msgdesc
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get All Field Meta Data Request (COM)
=====================================


``GetAllFiedlMetaData`` request provides all field metadata in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetAllFieldMetaData"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetAllFieldMetaData")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetAllFieldMetaData" Then
                                    
                    Dim md As Element
                    Dim e As Element
                    Dim numValues As Integer
                    Dim emsxFieldName As String
                    Dim emsxDispName As String
                    Dim emsxType As String
                    Dim emsxLevel As Integer
                    Dim emsxLen As Integer
                    
                    Set md = msg.GetElement("MetaData")
                        
                    numValues = md.numValues
                       
                    For i = 0 To numValues - 1
                    
                        Set e = md.GetValueAsElement(i)
                        
                        emsxFieldName = e.GetElement("EMSX_FIELD_NAME")
                        emsxDispName = e.GetElement("EMSX_DISP_NAME")
                        emsxType = e.GetElement("EMSX_TYPE")
                        emsxLevel = e.GetElement("EMSX_LEVEL")
                        emsxLen = e.GetElement("EMSX_LEN")
                           
                        log "MetaData: " & emsxFieldName & ", " & emsxDispName & ", " & emsxType & ", " & emsxLevel & ", " & emsxLen
                            
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get Broker Strategies With Asset Class Request (COM)
====================================================


``GetBrokerStrategiesWithAssetClass`` request provides all broker strategy fields with asset class data in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetBrokerStrategiesWithAssetClass"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetBrokerStrategiesWithAssetClass")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                req.Set "EMSX_ASSET_CLASS", "EQTY"  'one of EQTY, OPT, FUT or MULTILEG_OPT
                req.Set "EMSX_BROKER", "BMTB"
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetBrokerStrategiesWithAssetClass" Then
                                    
                    Dim strategies As Element
                    Dim strategy As String
                    Dim numValues As Integer
                    
                    Set strategies = msg.GetElement("EMSX_STRATEGIES")
                    
                    numValues = strategies.numValues
                    
                    log "Number of strategies: " & numValues
                    
                    For i = 0 To numValues - 1
                                            
                        strategy = strategies.GetValue(i)
                        log "Strategy: " & strategy
                    
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get Broker Strategy Info With Asset Class Request (COM)
========================================================


``GetBrokerStrategyInfoWithAssetClass`` request provides all broker strategy information fields with asset classdata in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetBrokerStrategyInfoWithAssetClass"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetBrokerStrategyInfoWithAssetClass")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                req.Set "EMSX_ASSET_CLASS", "EQTY"  'one of EQTY, OPT, FUT or MULTILEG_OPT
                req.Set "EMSX_BROKER", "BMTB"
                req.Set "EMSX_STRATEGY", "VWAP"
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetBrokerStrategyInfoWithAssetClass" Then
                                    
                    Dim strategies As Element
                    Dim e As Element
                    Dim numValues As Integer
                    Dim fieldName As String
                    Dim disable As String
                    Dim stringValue As String
                    
                    Set strategies = msg.GetElement("EMSX_STRATEGY_INFO")
                    
                    numValues = strategies.numValues
                    
                    log "Number of strategies: " & numValues
                    
                    For i = 0 To numValues - 1
                                            
                        Set e = strategies.GetValue(i)
                        
                        fieldName = e.GetElement("FieldName")
                        disable = e.GetElement("Disable")
                        stringValue = e.GetElement("StringValue")
                        
                        log "Strategy Info: " & fieldName & ", " & disable & ", " & stringValue
                    
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get Brokers With Asset Class Request (COM)
==========================================


``GetBrokersWithAssetClass`` request provides all broker information with asset class data in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetBrokersWithAssetClass"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetBrokersWithAssetClass")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                req.Set "EMSX_ASSET_CLASS", "EQTY"  'one of EQTY, OPT, FUT or MULTILEG_OPT
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetBrokersWithAssetClass" Then
                                    
                    Dim brokers As Element
                    Dim broker As String
                    Dim numValues As Integer
                    
                    Set brokers = msg.GetElement("EMSX_BROKERS")
                    
                    numValues = brokers.numValues
                    
                    For i = 0 To numValues - 1
                                            
                        broker = brokers.GetValue(i)
                        
                        log "Broker: " & broker
                    
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get Field Meta Data Request (COM)
=================================


``GetFieldMetaData`` request provides all field metadata in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetFieldMetaData"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetFieldMetaData")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                req.GetElement("EMSX_FIELD_NAMES").AppendValue "EMSX_TICKER"
                req.GetElement("EMSX_FIELD_NAMES").AppendValue "EMSX_P_A"
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetFieldMetaData" Then
                                    
                    Dim md As Element
                    Dim e As Element
                    Dim numValues As Integer
                    Dim emsxFieldName As String
                    Dim emsxDispName As String
                    Dim emsxType As String
                    Dim emsxLevel As Integer
                    Dim emsxLen As Integer
                    
                    Set md = msg.GetElement("MetaData")
                        
                    numValues = md.numValues
                       
                    For i = 0 To numValues - 1
                    
                        Set e = md.GetValueAsElement(i)
                        
                        emsxFieldName = e.GetElement("EMSX_FIELD_NAME")
                        emsxDispName = e.GetElement("EMSX_DISP_NAME")
                        emsxType = e.GetElement("EMSX_TYPE")
                        emsxLevel = e.GetElement("EMSX_LEVEL")
                        emsxLen = e.GetElement("EMSX_LEN")
                           
                        log "MetaData: " & emsxFieldName & ", " & emsxDispName & ", " & emsxType & ", " & emsxLevel & ", " & emsxLen
                            
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Get Teams Request (COM)
=======================


``GetTeams`` request provides all the team details in a response message.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GetTeams"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GetTeams")
        
                'req.Set "EMSX_REQUEST_SEQ", 1001
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GetTeams" Then
                                    
                    Dim teams As Element
                    Dim team As String
                    Dim numValues As Integer
                    
                    Set teams = msg.GetElement("TEAMS")
                        
                    numValues = teams.numValues
                       
                    For i = 0 To numValues - 1
                    
                        team = teams.GetValue(i)
                        
                        log "Team: " & team
                            
                    Next i
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Group Route Extended Request (COM)
==================================


``GroupRouteEx`` request submits an entire list as a single route to a basket/program broker strategy destination.

This request should only be used if the intention is to submit an entire list or basket of securities to a single broker strategy destination. This should not be confused with maintaining a list or a basket from a portfolio perspective.

Currently, this is a two-step process in EMSX API.  The first step is for the user will need to use ``CreateOrder`` request to create the order and add the ``EMSX_BASKET_NAME`` in the field. The second step is to submit the list using ``GroupRouteEx`` request and include the ``EMSX_SEQUENCE`` number inside the array. 


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - GroupRoute"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        Dim strategy As Element
        Dim indicator As Element
        Dim data As Element
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("GroupRouteEx")
                
                'Multiple order numbers can be added
                req.Append "EMSX_SEQUENCE", 3741540
                req.Append "EMSX_SEQUENCE", 3741541
                req.Append "EMSX_SEQUENCE", 3741542
        
                'The fields below are mandatory
                req.Set "EMSX_AMOUNT_PERCENT", 100
                req.Set "EMSX_BROKER", "BMTB"
                
                'For GroupRoute, the below values need to be added, but are taken
                'from the original order when the route is created.
                req.Set "EMSX_HAND_INSTRUCTION", "ANY"
                req.Set "EMSX_ORDER_TYPE", "MKT"
                req.Set "EMSX_TICKER", "IBM US Equity"  'Use to identify the asset class of all orders
                req.Set "EMSX_TIF", "DAY"

                'The fields below are optional
                'req.Set "EMSX_ACCOUNT", "TestAccount"
                'req.Set "EMSX_BOOKNAME", "HedgingBasket"
                'req.Set "EMSX_CFD_FLAG", "1"
                'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
                'req.Set "EMSX_CLEARING_FIRM", "FirmName"
                'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
                'req.Set "EMSX_GET_WARNINGS", "0"
                'req.Set "EMSX_GTD_DATE", "20170105"
                'req.Set "EMSX_LIMIT_PRICE", 123.45
                'req.Set "EMSX_LOCATE_BROKER", "BMTB"
                'req.Set "EMSX_LOCATE_ID", "SomeID"
                'req.Set "EMSX_LOCATE_REQ", "Y"
                'req.Set "EMSX_NOTES", "Some notes"
                'req.Set "EMSX_ODD_LOT", "0"
                'req.Set "EMSX_P_A", "P"
                'req.Set "EMSX_RELEASE_TIME", 34341
                'req.Set "EMSX_REQUEST_SEQ", 1001
                'req.Set "EMSX_STOP_PRICE", 123.5
                'req.Set "EMSX_TRADER_UUID", 1234567
               
                'Set the Request Type if this is for multi-leg orders
                'only valid for options
                
                'Dim requestType As Element
                '
                'requestType = req.GetElement("EMSX_REQUEST_TYPE")
                'requestType.SetChoice "Multileg"
                '
                'Dim multileg As Element
                'multileg = requestType.GetElement("Multileg")
                'multileg.SetElement "EMSX_AMOUNT", 10
                'multileg.GetElement("EMSX_ML_RATIO").AppendValue 2
                'multileg.GetElement("EMSX_ML_RATIO").AppendValue 3

                'Add the Route Ref ID values
                Dim routeRefIDPairs As Element
                Set routeRefIDPairs = req.GetElement("EMSX_ROUTE_REF_ID_PAIRS")
                        
                Dim route1 As Element
                Set route1 = routeRefIDPairs.AppendElment()
                route1.SetElement "EMSX_ROUTE_REF_ID", "MyRouteRef1"
                route1.SetElement "EMSX_SEQUENCE", 3741540
                        
                Dim route2 As Element
                Set route2 = routeRefIDPairs.AppendElment()
                route2.SetElement "EMSX_ROUTE_REF_ID", "MyRouteRef2"
                route2.SetElement "EMSX_SEQUENCE", 3741541
                        
                Dim route3 As Element
                Set route3 = routeRefIDPairs.AppendElment()
                route3.SetElement "EMSX_ROUTE_REF_ID", "MyRouteRef3"
                route3.SetElement "EMSX_SEQUENCE", 3741542
                        
                'Below we establish the strategy details. Strategy details
                'are common across all orders in a GroupRoute operation.
                Set strategy = req.GetElement("EMSX_STRATEGY_PARAMS")
                strategy.SetElement "EMSX_STRATEGY_NAME", "VWAP"
                
                Set indicator = strategy.GetElement("EMSX_STRATEGY_FIELD_INDICATORS")
                Set data = strategy.GetElement("EMSX_STRATEGY_FIELDS")
                            
                'Strategy parameters must be appended in the correct order. See the output
                'of GetBrokerStrategyInfo request for the order. The indicator value is 0 for
                'a field that carries a value, and 1 where the field should be ignored
                
                data.AppendElment().SetElement "EMSX_FIELD_DATA", "09:30:00"    'StartTime
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 0

                data.AppendElment().SetElement "EMSX_FIELD_DATA", "10:30:00"   'EndTime
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 0

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'Max%Volume
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           '%AMSession
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'OPG
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'MOC
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'CompletePX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1
                        
                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'TriggerPX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'DarkComplete
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'DarkCompPX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'RefIndex
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'Discretion
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1
                
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "GroupRouteEx" Then
                    
                    Dim numValues As Integer
                    Dim emsxSequence As Long
                    Dim emsxRouteId As Long
                    Dim e As Element
                    
                    If msg.AsElement.HasElement("EMSX_SUCCESS_ROUTES") Then
                    
                        Dim success As Element
                        
                        Set success = msg.GetElement("EMSX_SUCCESS_ROUTES")

                        numValues = success.numValues
                                
                        For i = 0 To numValues - 1
                                    
                            Set e = success.GetValueAsElement(i)
                            
                            emsxSequence = e.GetElement("EMSX_SEQUENCE")
                            emsxRouteId = e.GetElement("EMSX_ROUTE_ID")
                            
                            log "Success: " & emsxSequence & ", " & emsxRouteId
                        Next i
                    End If
                    
                    If msg.AsElement.HasElement("EMSX_FAILED_ROUTES") Then
                        
                        Dim failed As Element
                        
                        Set failed = msg.GetElement("EMSX_FAILED_ROUTES")

                        numValues = failed.numValues
                                
                        For i = 0 To numValues - 1
                                    
                            Set e = failed.GetValueAsElement(i)
                            
                            emsxSequence = e.GetElement("EMSX_SEQUENCE")
                            errorCode = e.GetElement("ERROR_CODE")
                            errorMessage = e.GetElement("ERROR_MESSAGE")
                            
                            log "Failed: " & emsxSequence & ", " & errorCode & ", " & errorMessage
                            
                        Next i
                    End If
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub

Modify Order Request (COM)
==========================


``ModifyOrder`` request modifies an existing or previously created order in EMSX<GO> or using EMSX API. 


.. code-block:: vb.net



Modify Route Extended Request (COM)
===================================


``ModifyRouteEx`` request modifies an existing or previously created child routes in EMSX<GO> or using EMSX API. 


.. code-block:: vb.net


Route Extended Request (COM)
============================


``RouteEx`` request submits an existing order into various execution veneues. This request is used primarily to submit a child route based on previously created parent order.


 .. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - Route"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("RouteEx")
        
                'The fields below are mandatory
                req.Set "EMSX_SEQUENCE", 3741193
                req.Set "EMSX_AMOUNT", 500
                req.Set "EMSX_BROKER", "BB"
                req.Set "EMSX_HAND_INSTRUCTION", "ANY"
                req.Set "EMSX_ORDER_TYPE", "MKT"
                req.Set "EMSX_TICKER", "IBM US Equity"
                req.Set "EMSX_TIF", "DAY"
                
                'The fields below are optional
                'req.Set "EMSX_ACCOUNT", "TestAccount"
                'req.Set "EMSX_CFD_FLAG", "1"
                'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
                'req.Set "EMSX_CLEARING_FIRM", "FirmName"
                'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
                'req.Set "EMSX_GET_WARNINGS", "0"
                'req.Set "EMSX_GTD_DATE", "20170105"
                'req.Set "EMSX_LIMIT_PRICE", 123.45
                'req.Set "EMSX_LOCATE_BROKER", "BMTB"
                'req.Set "EMSX_LOCATE_ID", "SomeID"
                'req.Set "EMSX_LOCATE_REQ", "Y"
                'req.Set "EMSX_NOTES", "Some notes"
                'req.Set "EMSX_ODD_LOT", "0"
                'req.Set "EMSX_P_A", "P"
                'req.Set "EMSX_RELEASE_TIME", 34341
                'req.Set "EMSX_REQUEST_SEQ", 1001
                'req.Set "EMSX_ROUTE_REF_ID", "UniqueID"
                'req.Set "EMSX_STOP_PRICE", 123.5
                'req.Set "EMSX_TRADER_UUID", 1234567
               
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "Route" Then
                    
                    Dim emsxSequence As Long
                    Dim emsxRouteId As Integer
                    Dim msgdesc As String
                    
                    emsxSequence = msg.GetElement("EMSX_SEQUENCE")
                    emsxRouteId = msg.GetElement("EMSX_ROUTE_ID")
                    msgdesc = msg.GetElement("MESSAGE")
                    
                    log "EMSX_SEQUENCE: " & emsxSequence & "    EMSX_ROUTE_ID: " & emsxRouteId & "    MESSAGE: " & msgdesc
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



Route Manually Request (COM)
============================


``RouteManuallyEx`` requestis generally used for phone orders where the placement is external to EMSX API. This request creates an order and notifies EMSX<GO> that this order is routed to the execution venue.


.. code-block:: vb.net

    Option Explicit

    Private WithEvents m_BBG_EMSX As blpapicomLib2.Session
    Public running As Boolean
    Private svc As blpapicomLib2.service
    Private emsxService As String
    Private requestID As blpapicomLib2.CorrelationId

    Private Sub Class_Initialize()

        log "Bloomberg - EMSX API Example - RouteManually"

        emsxService = "//blp/emapisvc_beta"
        
        Set m_BBG_EMSX = New blpapicomLib2.Session
        
        running = True
        
        m_BBG_EMSX.QueueEvents = True
        m_BBG_EMSX.Start
        

    End Sub

    Private Sub Class_Terminate()
        Set m_BBG_EMSX = Nothing
    End Sub

    Private Sub m_BBG_EMSX_ProcessEvent(ByVal obj As Object)

        On Error GoTo errHandler

        Dim eventObj As blpapicomLib2.Event
        
         '   Assign the returned data to a Bloomberg type event
        Set eventObj = obj
        
        If Application.Ready Then
        
            Select Case eventObj.EventType
            
                Case SESSION_STATUS
                    processSessionEvent eventObj
                    
                Case BLPSERVICE_STATUS
                    processServiceEvent eventObj
                    
                Case RESPONSE
                    processResponseEvent eventObj
                    
            End Select
            
        End If

        Exit Sub

    errHandler:
        Dim errmsg As Variant
        errmsg = Err.Description
        log (errmsg)
        running = False

    End Sub


    Private Sub processSessionEvent(evt As blpapicomLib2.Event)

        log "Processing SESSION_STATUS event"
        
        Dim it As blpapicomLib2.MessageIterator
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "SessionStarted" Then
                log "Session started..."
                m_BBG_EMSX.OpenService emsxService
            ElseIf msg.MessageTypeAsString = "SessionStartupFailure" Then
                log "Error: Session startup failed"
                running = False
            End If
            
        Loop

    End Sub

    Private Sub processServiceEvent(evt As blpapicomLib2.Event)

        Dim req As REQUEST
        Dim service As service
        Dim strategy As Element
        Dim indicator As Element
        Dim data As Element
        Dim it As blpapicomLib2.MessageIterator
        
        On Error GoTo failed
        
        log "Processing SERVICE_STATUS event"
        
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If msg.MessageTypeAsString = "ServiceOpened" Then
        
                ' Get the service
                Set service = m_BBG_EMSX.GetService(emsxService)
        
                'First, create our request object
                Set req = service.CreateRequest("RouteManuallyEx")
        
                'The fields below are mandatory
                req.Set "EMSX_SEQUENCE", 3741495
                req.Set "EMSX_AMOUNT", 500
                req.Set "EMSX_BROKER", "BB"
                req.Set "EMSX_HAND_INSTRUCTION", "ANY"
                req.Set "EMSX_ORDER_TYPE", "MKT"
                req.Set "EMSX_TICKER", "IBM US Equity"
                req.Set "EMSX_TIF", "DAY"
                
                'The fields below are optional
                'req.Set "EMSX_ACCOUNT", "TestAccount"
                'req.Set "EMSX_BOOKNAME", "HedgingBasket"
                'req.Set "EMSX_CFD_FLAG", "1"
                'req.Set "EMSX_CLEARING_ACCOUNT", "ClrAccName"
                'req.Set "EMSX_CLEARING_FIRM", "FirmName"
                'req.Set "EMSX_EXEC_INSTRUCTIONS", "AnyInst"
                'req.Set "EMSX_GET_WARNINGS", "0"
                'req.Set "EMSX_GTD_DATE", "20170105"
                'req.Set "EMSX_INVESTOR_ID", "InvID"
                'req.Set "EMSX_LIMIT_PRICE", 123.45
                'req.Set "EMSX_LOCATE_BROKER", "BMTB"
                'req.Set "EMSX_LOCATE_ID", "SomeID"
                'req.Set "EMSX_LOCATE_REQ", "Y"
                'req.Set "EMSX_NOTES", "Some notes"
                'req.Set "EMSX_ODD_LOT", "0"
                'req.Set "EMSX_ORDER_REF_ID", "UniqueID"
                'req.Set "EMSX_P_A", "P"
                'req.Set "EMSX_RELEASE_TIME", 34341
                'req.Set "EMSX_REQUEST_SEQ", 1001
                'req.Set "EMSX_ROUTE_REF_ID", "UniqueID"
                'req.Set "EMSX_STOP_PRICE", 123.5
                'req.Set "EMSX_TRADER_UUID", 1234567
               
                'Below we establish the strategy details. Strategy details
                'are common across all orders in a GroupRoute operation.
                Set strategy = req.GetElement("EMSX_STRATEGY_PARAMS")
                strategy.SetElement "EMSX_STRATEGY_NAME", "VWAP"
                
                Set indicator = strategy.GetElement("EMSX_STRATEGY_FIELD_INDICATORS")
                Set data = strategy.GetElement("EMSX_STRATEGY_FIELDS")
                            
                'Strategy parameters must be appended in the correct order. See the output
                'of GetBrokerStrategyInfo request for the order. The indicator value is 0 for
                'a field that carries a value, and 1 where the field should be ignored
                
                data.AppendElment().SetElement "EMSX_FIELD_DATA", "09:30:00"    'StartTime
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 0

                data.AppendElment().SetElement "EMSX_FIELD_DATA", "10:30:00"   'EndTime
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 0

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'Max%Volume
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           '%AMSession
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'OPG
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'MOC
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'CompletePX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1
                        
                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'TriggerPX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'DarkComplete
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'DarkCompPX
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'RefIndex
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1

                data.AppendElment().SetElement "EMSX_FIELD_DATA", ""           'Discretion
                indicator.AppendElment().SetElement "EMSX_FIELD_INDICATOR", 1
               
                log "Request: " & req.Print
                
                ' Send the request
                Set requestID = m_BBG_EMSX.SendRequest(req)

            ElseIf msg.MessageTypeAsString = "ServiceOpenFailure" Then
            
                log "Error: Service failed to open"
                running = False
                
            End If
            
        Loop

        Exit Sub
        
    failed:

        log "Failed to send the request: " + Err.Description
        
        running = False
        Exit Sub
        
    End Sub

    Private Sub processResponseEvent(evt As blpapicomLib2.Event)

        log "Processing RESPONSE event"
        
        Dim it As blpapicomLib2.MessageIterator
        Dim i As Integer
        Dim errorCode As Long
        Dim errorMessage As String
     
        Set it = evt.CreateMessageIterator()

        ' Loop while we have messages remaining
        Do While it.Next()
                  
            Dim msg As Message
            
            '   Pick up message
            Set msg = it.Message
            
            log "MessageType: " + msg.MessageTypeAsString
            
            If evt.EventType = RESPONSE And msg.CorrelationId.Value = requestID.Value Then
            
                If msg.MessageTypeAsString = "ErrorInfo" Then
                
                    errorCode = msg.GetElement("ERROR_CODE")
                    errorMessage = msg.GetElement("ERROR_MESSAGE")
                    
                    log "ERROR CODE: " & errorCode & "    ERROR DESCRIPTION: " & errorMessage
                
                    running = False
                    
                ElseIf msg.MessageTypeAsString = "RouteManually" Then
                    
                    Dim emsxSequence As Long
                    Dim emsxRouteId As Integer
                    Dim msgdesc As String
                    
                    emsxSequence = msg.GetElement("EMSX_SEQUENCE")
                    emsxRouteId = msg.GetElement("EMSX_ROUTE_ID")
                    msgdesc = msg.GetElement("MESSAGE")
                    
                    log "EMSX_SEQUENCE: " & emsxSequence & "    EMSX_ROUTE_ID: " & emsxRouteId & "    MESSAGE: " & msgdesc
                    
                    m_BBG_EMSX.Stop
                    running = False
                
                End If
            End If
        Loop

    End Sub



