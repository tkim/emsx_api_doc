##########################################
Get Brokers With Asset Class Request (COM)
##########################################

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








