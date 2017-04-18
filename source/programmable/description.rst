#######################################
Description of Request/Response Service
#######################################


The request/response service can be used for both buy-side EMSX<GO> workflows or sell-side EMSX to EMSX (E2E) workflows.

The buy-side EMSX request/response supports all the basic buy-side execution management control via request/response service where the sell-side EMSX request/response supports additional sell-side workflow for acknowledging or rejecting an order coming in via E2E workflow.


Buy-Side Request/Response Service
=================================

EMSX API supports the following buy-side Request/Response services:-

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


Sell-Side Request/Response Service
==================================


EMSX API supports the following sell-side Request/Response services:-

*Please note, the descriptions to the legacy request/response services are omitted from the description section.*


=================================== =================================================================
Request Name             			Action
=================================== =================================================================
``ManualFill``						Request to manually fill a child route.
``SellSideAck`` 					Request to acknowlede an order on EMSX to EMSX setting.
``SellSideReject`` 					Request to reject an order on EMSX to EMSX setting.
=================================== =================================================================


.. note::

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


All date format except ``EMSX_QUEUED_TIME`` are in yyyymmdd format. All time format except ``EMSX_STRATEGY_END_TIME`` and ``
EMSX_STRATEGY_START_TIME`` are in number of seconds from midnight.

The Strategy time zone is set using the EMSX<GO> function in the Bloomberg terminal under Routing Defaults section inside 
the Settings menu. In the Routing Defaults, the user can select Exchange vs. User time zone for strategy time zone.


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


Custom Notes & Free Text Fields
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


			
