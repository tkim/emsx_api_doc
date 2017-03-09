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
