MiFID II
========


In preparation for the MiFID II, Bloomberg EMSX API is enhancing its workflow to provide clients with the 
needed data and solutions to fulfill MiFID II obligations (Trade reporting, Transaction reporting, best 
execution and order record keeping). Bloomberg EMSX API as part of Bloomberg EMSX will connect to 
Bloomberg RHUB to support client's regulatory obligations through a centralized access for ARM, APA, ORK (
Bvalut) and best execution (BTCA).


=========================================== ======= 
MiFID II Field Names 						Type	
------------------------------------------- ------- 
``EMSX_BUYSIDE_LEI``						String	
``EMSX_BROKER_LEI``							String  
``EMSX_TRADE_REPORTING_INDICATOR``			String	
``EMSX_TRANSACTION_REPORTING_MIC``			String	
``EMSX_APA_MIC``							String	
``EMSX_OTC_FLAG``							String
``EMSX_WAIVER_FLAG``						String
``EMSX_LAST_CAPACITY``						String
``EMSX_CLIENT_IDENTIFICATION``				String
``EMSX_SI``									Bool
``EMSX_MIFID_II_INSTRUCTION``				String
``EMSX_BROKER_SI``							String	
``EMSX_ROUTE_CREATE_TIME_MICROSEC``			Float64	
``EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC``	Float64	
``EMSX_TIME_STAMP_MICROSEC``				Float64
``EMSX_QUEUED_TIME_MICROSEC``				Float64	
``EMSX_LAST_FILL_TIME_MICROSEC``			Float64	
``EMSX_GPI``								String	
=========================================== ======= 


New time stamp elements:-

===================================  ========================================
``EMSX_ROUTE_CREATE_TIME_MICROSEC``  ``EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC``
``EMSX_TIME_STAMP_MICROSEC``         ``EMSX_QUEUED_TIME_MICROSEC`` 
``EMSX_LAST_FILL_TIME_MICROSEC`` 
===================================  ========================================


.. important::

	The new timestamps for the new elements are only microseconds if they extend out to full digits. 
	(e.g. 0.000001) 

	From time to time they will be printed to the millisecond precision in cases when the microsecond 
	timestamp from the back-end is not available. (e.g. 0.001)  	


New requests with the MiFID II elements:-

============================ =========================
``CreateOrder``              ``CreateOrderAndRouteEx`` 
``GroupRouteRequestEx``      ``ManualFill``
``ManualRouteEx``            ``ModifyOrderRequestEx`` 
``ModifyTheRouteRequestEx``  ``RouteOrderRequestsEx``
============================ =========================


Order subscription with the MiFID II elements:- 

============================= ==============================
``EMSX_BUYSIDE_LEI``          ``EMSX_CLIENT_IDENTIFICATION`` 
``EMSX_GPI``                  ``EMSX_MIFID_II_INSTRUCTION``
``EMSX_QUEUED_TIME_MICROSEC`` ``EMSX_SI``                    
``EMSX_TIME_STAMP_MICROSEC``  
============================= ==============================


Route subscription with the MiFID II elements:- 

======================================== ===================================
``EMSX_APA_MIC``.                        ``EMSX_BUYSIDE_LEI``.         
``EMSX_BROKER_LEI``                      ``EMSX_BROKER_SI``
``EMSX_CLIENT_IDENTIFICATION``           ``EMSX_GPI``          
``EMSX_LAST_CAPACITY``.                  ``EMSX_LAST_FILL_TIME_MICROSEC``
``EMSX_MIFID_II_INSTRUCTION``            ``EMSX_OTC_FLAG``
``EMSX_QUEUED_TIME_MICROSEC``            ``EMSX_ROUTE_CREATE_TIME_MICROSEC``
``EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC`` ``EMSX_TRADE_REPORTING_INDICATOR`` 
``EMSX_TRANSACTION_REPORTING_MIC``       ``EMSX_TIME_STAMP_MICROSEC``
``EMSX_WAIVER_FLAG``
======================================== ===================================


.. note::

	MiFID II acrynom definitions can be found in `Glossary`_ section of the document.

	.. _Glossary: http://emsx-api-doc.readthedocs.io/en/latest/glossary.html

