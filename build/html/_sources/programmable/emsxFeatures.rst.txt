EMSX Features
=============
The EMSX API supports 99% of the features supported in ``EMSX<GO>`` function. 

One of the few exceptions to this is the EMSX Pairs Ticket. Currently, the EMSX API does not support the EMSX Pairs Ticket features. 
However, most EMSX API users are still able to trade pairs using the old EMSX ticket logic or by incorporating their own pairs logics on top of the EMSX API.


EMSX Teams
==========
The EMSX API allows the same action on ``TEAMVIEW`` as you would have permission on ``EMSX<GO>`` function.

The ``TEAMVIEW`` feature in ``EMSX<GO>`` allows a team member to view or take action on behalf of the team members based on the team setting within ``EMSX<GO>``.

For EMSX API, This offers flexibilities within the application design. For example, a single subscription with the team name can capture all the events for the team members. The topic string for using team remains the same as non-team with the exception of adding team name on the topic string as illustrated below.

.. important::

	//blp/emapisvc_beta/order;team=my_team_name?fields=EMSX_ASSIGNED_TRADER, EMSX_BASKET_NAME, EMSX_CFD_FLAG, EMSX_AMOUNT


Trading on behalf of team members from ``TEAMVIEW`` requires creating a route on behalf of the team member. The service object of type ``RouteEx`` and fill in the required fields before submitting the request.

Within ``RouteEx``, there is an element ``EMSX_TRADER_UUID`` where the user can enter the order owner's Bloomberg UUID. Bloomberg will do the validation against the user privilege setup via 
``EMT<GO>`` and ``EMBR<GO>``.

A user can be part of more than one team on the backend. When the user creates the topic string and does not belong to a team or specify a wrong team,  the user will receive an error.

In cases where a user is defined as a member of multiple teams, then the user will need to supply multiple subscriptions. (One for each team). These subscriptions should be monitored separately since the user will receive two notifications. 

.. important::

	It's best to keep the overall design simple. TEAM is a heirarchical structure and thus best to have a single order and single route subscription for the entire TEAM strucuture and avoid replication. The replication increases the bandwidth usage and provides ZERO benefit for the end client.



EMSX Element Definitions
========================
For information on accessing field meta data, this is currently only supported within Bloomberg terminal.

The user will need to access ``FLDS<GO>`` function within the Bloomberg terminal. Once in ``FLDS<GO>``, type EMSX underneath the security section and choose EMSX under the filter. The source is Calcrt and should select All for Field Type.


.. image:: /image/flds.jpg


EMSX Element Definition (A to M)
================================

The EMSX element definitions will include the type of the element and will inform whether the element is an ``ORDER``, ``ROUTE``, or sometimes both ``O,R`` elements. The type consists of ``INT64``, ``INT32``, ``STRING``, and ``FLOAT64``.

+-----------------------------+------------------------------------------------------------------------+
|Field                        |Definition                                                              |
+=============================+========================================================================+
|``API_SEQ_NUM``              |  | ``INT64`` Special field to indicate the sequence number of the API  |
|                             |  | events. The number begins at 1 and increases with each event posted |
|                             |  | to a client subscription. It can be used by the client side to      |
|                             |  | guarantee order, and to identify any gaps in subscription events.   |       
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ACCOUNT``             |  | ``STRING`` ``O,R`` The account of the routing firm as designated by |
|                             |  | the broker chosen. This field is applicable to trades on an order   |
|                             |  | and/or route level, and does not populate on a per security basis.  |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_AMOUNT``              |  | ``INT32`` ``O,R`` The total amount of the order or route. This field|
|                             |  | is applicable to trades on an order and/or route level, and does not|
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_APA_MIC``             |  | ``STRING`` ``ROUTE`` Approved publication arrangement in MiFID II.  |
|                             |  | This is a route level field.                                        |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ARRIVAL_PRICE``       |  | ``FLOAT64`` ``ROUTE`` The arrival price benchmark for order.The     | 
|                             |  | arrival price is the midpoint during market hours, and the next     |   
|                             |  | opening price between exchange sessions. This field is applicable to|
|                             |  | trades on an order level, and does not populate on a per security   |
|                             |  | basis.                                                              |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ASSET_CLASS``         |  | ``STRING`` ``STATIC`` ``ORDER`` The asset class of the order. This  |
|                             |  | field is applicable to trades on an order level, and does not       |
|                             |  | populate on a per security basis. This is a static field.           | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ASSIGNED_TRADER``     |  | ``STRING`` ``ORDER`` The name of the trader assigned to the order.  |
|                             |  | This field is applicable to trades on an order level, and does not  |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_AVG_PRICE``           |  | ``FLOAT64`` ``O,R`` The average price for one share executed with   |
|                             |  | the order, calculated over the life of the order. This field is     |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BASKET_NAME``         |  | ``STRING`` ``ORDER`` The name assigned to a group of related orders |
|                             |  | contained in a basket. This field is applicable to trades on an     |
|                             |  | order level, and does not populate on a per security basis.         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BASKET_NUM``          |  | ``INT32`` ``ORDER`` The number corresponding to the                 |
|                             |  | ``EMSX_BASKET_NAME`` assigned to a group of related orders.         |
|                             |  | This field is applicable to trades on an order level, and does not  |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BLOCK_ID``            |  | ``STRING`` ``ORDER`` The EMSX Block ID                              |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER``              |  | ``STRING`` ``O,R`` The code for the broker with whom the order is   |
|                             |  | routed. This field is applicable to trades on an order and/or route |
|                             |  | level, and does not populate on a per security basis.               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER_COMM``         |  | ``FLOAT64`` ``O,R`` The amount of commission charged by the broker  |
|                             |  | for the order or route. This field is applicable to trades on an    |
|                             |  | order and/or route level, and does not populate on a per security   |
|                             |  | basis.                                                              |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER_LEI``          |  | ``STRING`` ``ROUTE`` Broker Legal Entity Identifier in MiFID II.    |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER_SI``           |  | ``STRING`` ``ROUTE`` Broker Systematic Internalizer in MiFID II.    |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER_STATUS``       |  | ``STRING`` ``ROUTE`` Broker status in EMSX.                         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BSE_AVG_PRICE``       |  | ``FLOAT64`` ``STATIC`` ``O,R`` The EMSX Bombay Stock Exchange       |
|                             |  | Average Price. Average price of the fills completed for the order or| 
|                             |  | route on the Bombay Stock Exchange (BSE). This field is applicable  |
|                             |  | to trades on an order and/or route level, and does not populate     |
|                             |  | on a per security basis.                                            | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BSE_FILLED``          |  | ``INT32`` ``O,R`` The EMSX Bombay Stock Exchange Filled.  Total     |
|                             |  | quantity of the fills completed for the order or route on the Bombay|
|                             |  | Stock Exchange (BSE). This field is applicable to trades on an order|
|                             |  | and/or route level, and does not populate on a per security basis.  | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BUYSIDE_LEI``         |  | ``STRING`` ``O,R`` The buyside Legal Entity Identifier in MiFID II. |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CFD_FLAG``            |  | ``STRING`` ``ORDER`` The EMSX Contract For Difference Flag.         |
|                             |  | Indicates if the order is a contract for differences (CFD) trade.   |
|                             |  | This field is applicable to trades on an order level, and does not  |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CLEARING_ACCOUNT``    |  | ``STRING`` ``ROUTE`` The clearing account defined on a futures or   |
|                             |  | option route. This field is applicable to trades on a route level,  |
|                             |  | and does not populate on a per security basis.                      | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CLEARING_FIRM``       |  | ``STRING`` ``ROUTE`` The clearing firm defined on a futures or      |
|                             |  | options route. This field is applicable to trades on a route level, |
|                             |  | and does not populate on a per security basis.                      |
+-----------------------------+------------------------------------------------------------------------+

+------------------------------+-----------------------------------------------------------------------+
|``EMSX_CLIENT_IDENTIFICATION``| | ``STRING`` ``O,R`` MiFID II field for client Identification.        |
+------------------------------+-----------------------------------------------------------------------+

+-----------------------------+------------------------------------------------------------------------+
|``EMSX_COMM_DIFF_FLAG``      |  | ``STRING`` ``O,R`` The EMSX Commission Difference between broker    |
|                             |  | commission and AIM (Asset and Investment Manager) commission values.|
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_COMM_RATE``           |  | ``FLOAT64`` ``O,R`` The EMSX Commission Rate of commission charged  |
|                             |  | on the trade. This field is applicable to trades on an order and/or |
|                             |  | route level, and does not populate on a per security basis.         | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CURRENCY_PAIR``       |  | ``STRING`` ``STATIC`` ``O,R`` The EMSX Currency Pair which provides |
|                             |  | the spot rate to convert the security's currency and the user's     |
|                             |  | currency. This field is applicable to trades on an order and/or     | 
|                             |  | route level, and does not populate on a per security basis.         |  
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CUSTOM_ACCOUNT``      |  | ``STRING`` ``ROUTE`` The EMSX Route Account, is the account value at|
|                             |  | the level of the route. This field is applicable to trades on a     |
|                             |  | route level, and does not populate on a per security basis.         | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CUSTOM_NOTEn``        |  | ``STRING`` ``ORDER`` 79-character free text field.                  |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DATE``                |  | ``INT32`` ``ORDER`` The EMSX Order Creation Date is the date on     |
|                             |  | which the order is created. This field is applicable to trades on an|
|                             |  | order level, and does not populate on a per security basis.         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DAY_AVG_PRICE``       |  | ``FLOAT64`` ``O,R`` The EMSX Day Average Price is the average price |
|                             |  | for one share executed with the order, based on shares filled today.|
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DAY_FILL``            |  | ``INT32`` ``O,R`` The EMSX Day Fill is the total quantity of shares |
|                             |  | filled today for this order/security, across any number of brokers. |
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DIR_BROKER_FLAG``     |  | ``STRING`` ``ORDER`` The EMSX Directed Brokers is an indicator of   |
|                             |  | whether the order has funds with the directed brokers defined. This |
|                             |  | field is applicable to trades on an order level, and does not       |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXCHANGE``            |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Exchange is the exchange   |
|                             |  | code for the order where the security in the order is listed. This  |
|                             |  | field is applicable to trades on an order level, and does not       |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXCHANGE_DESTINATION``|  | ``STRING`` ``O,R`` The EMSX Exchange Destination is the Exchange    |
|                             |  | destination of the security for the order or route. This field is   |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXEC_INSTRUCTION``    |  | ``STRING`` ``O,R`` The EMSX execution instruction field.            |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXECUTE_BROKER``      |  | ``STRING`` ``ROUTE`` The EMSX Execution Broker is the executing     |
|                             |  | broker on the trade for the route. This field is applicable to      |
|                             |  | trades on a route level, and does not populate on a per security    | 
|                             |  | basis.                                                              | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_FILL_ID``             |  | ``INT32`` ``STATIC`` ``O,R`` The fill number associated with a      |
|                             |  | route. This field is applicable to trades on an order and/or route  |
|                             |  | level, and does not populate on a per security basis.               | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_FILLED``              |  | ``INT32`` ``O,R`` The quantity of shares which have been executed by|
|                             |  | broker. This field is applicable to trades on an order and/or route | 
|                             |  | level, and does not populate on a per security basis.               | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_GPI``                 |  | ``STRING`` ``O,R`` The Global Personal Identifier in MiFID II.      |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_GTD_DATE``            |  | ``INT32`` ``O,R`` The EMSX Good to Date is the date the order is in |
|                             |  | force until, based on local exchange date and time. This field is   |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_HAND_INSTRUCTION``    |  | ``STRING`` ``O,R`` The EMSX Handling Instruction is the instructions|
|                             |  | for handling the order or route. The values can be preconfigured or |
|                             |  | a value customized by the broker. This field is applicable to trades|
|                             |  | on an order and/or route level, and does not populate on a per      |
|                             |  | security basis.                                                     |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_IDLE_AMOUNT``         |  | ``STRING`` ``ORDER`` The quantity of shares yet to be routed or     |
|                             |  | executed, equal to the order quantity minus amounts filled,         |
|                             |  | unreleased, and routed. This field is applicable to trades on an    |
|                             |  | order level, and does not populate on a per security basis.         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_INVESTOR_ID``         |  | ``STRING`` ``ORDER`` The identifier for the buy side investor as    |
|                             |  | used for markets such as Korea and Taiwan. This field is applicable |
|                             |  | to trades on an order level, and does not populate on a per security| 
|                             |  | basis.                                                              | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_IS_MANUAL_ROUTE``     |  | ``INT32`` ``STATIC`` ``ROUTE`` The EMSX Manual Route indicates that |
|                             |  | the route was not communicated  electronically to the broker. This  |
|                             |  | field is applicable to trades on a route level, and does not        |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ISIN``                |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX International Securities   |
|                             |  | Identification Number or the ISIN (International Securities         |
|                             |  | Identification Number) of the security in the order. This field is  | 
|                             |  | applicable to trades on an order level, and does not populate on a  |
|                             |  | per security basis.                                                 |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_CAPACITY``       |  | ``STRING`` ``ROUTE`` The broker capacity in order execution.        |
|                             |  | (e.g. agent, cross as agent, cross as principal, and principal)     |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_FILL_DATE``      |  | ``INT32`` ``ROUTE`` The date of the last fill based on the user's   |
|                             |  | time zone. This field is applicable to trades on a route level, and |
|                             |  | does not populate on a per security basis.                          | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_FILL_TIME``      |  | ``INT32`` ``ROUTE`` The time of the last fill based on seconds from |
|                             |  | midnight in the user's time zone. This field is applicable to trades|
|                             |  | on a route level, and does not populate on a per security basis.    |
+-----------------------------+------------------------------------------------------------------------+

+--------------------------------+---------------------------------------------------------------------+
|``EMSX_LAST_FILL_TIME_MICROSEC``|  | ``INT32`` ``ROUTE`` The last fill time based on the user's time  |
|                                |  | zone in microseconds. This field is applicable to trades on a    |
|                                |  | route level, and does not populate on a per security basis.      |  
+--------------------------------+---------------------------------------------------------------------+

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
|``EMSX_LEG_FILL_DATE_ADDED`` |  | ``INT32`` ``ROUTE`` The date added for the leg fill.                |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_PRICE``      |  | ``FLOAT64`` ``ROUTE`` The leg fill price.                           |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_SEQ_NO``     |  | ``INT32`` ``ROUTE`` The leg fill sequence number.                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_SHARES``     |  | ``FLOAT64````ROUTE`` The leg fill shares.                           |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_SIDE``       |  | ``STRING`` ``ROUTE`` The leg fill side.                             |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_TICKER``     |  | ``STRING`` ``ROUTE`` The leg fill ticker.                           |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LEG_FILL_TIME_ADDED`` |  | ``INT32`` ``ROUTE`` The time added for the leg fill.                | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LIMIT_PRICE``         |  | ``FLOAT64`` ``O,R`` The price which is the maximum the order to buy |
|                             |  | securities or commodities should be executed at; or the minimum at  |
|                             |  | which securities or commodities should be sold. This field is       |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_MIFID_II_INSTRUCTION``|  | ``STRING`` ``O,R`` The MiFID II instruction field.                  |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_MISC_FEES``           |  | ``FLOAT64`` ``ROUTE`` The EMSX Miscellaneous Fees is the assorted   |
|                             |  | fees associated with a trade, such as regulatory fees and taxes.    | 
|                             |  | This field is applicable to trades on a route level, and does not   |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_MOD_PEND_STATUS``     |  | ``STRING`` ``ORDER`` Only valid for Sell-Side EMSX on E2E           |
|                             |  | (EMSX to EMSX) settings. Fields that can populate: Size, Price,     |
|                             |  | Stop, GTDDate, TIF, Type and instruments.                           |
|                             |  | e.g. EMSX_MOD_PEND_STATUS= "Pending Info|Size: 500.0 -> 200.0|      |
|                             |  | Price 2.0000 -> 4.0000|Instr: -> test instr"                        |
+-----------------------------+------------------------------------------------------------------------+


Multi-Leg Element Definition
============================


+--------------------------+---------------------------------------------------------------------------+
|Field                     |Definition                                                                 |
+==========================+===========================================================================+
|``EMSX_ML_ID``            |  | ``STRING`` ``ROUTE`` The multi-leg ID.                                 |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_LEG_QUANTITY``  |  | ``INT32`` ``ROUTE`` The EMSX Multi-Leg Shares per Leg is the number of |
|                          |  | shares per leg in the multi-leg strategy. This field is applicable to  |
|                          |  | trades on a route level, and does not populate on a per security basis.|
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_NUM_LEGS``      |  | ``INT32`` ``ROUTE`` The EMSX Multi-Leg Number Legs is the number of    |
|                          |  | legs in the multi-leg strategy. This field is applicable to trades on  |
|                          |  | a route level, and does not populate on a per security basis.          |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_PERCENT_FILLED``|  | ``FLOAT64`` ``ROUTE`` The EMSX Multi-Leg Percent Filled is the percent |
|                          |  | of legs filled in a multi-leg strategy. This field is applicable to    |
|                          |  | trades on a route level, and does not populate on a per security basis.|
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_RATIO``         |  | ``FLOAT64`` ``ROUTE`` The EMSX Multi-Leg Ratio is the factor that      |
|                          |  | controls the number of securities in each leg. This field is applicable|
|                          |  | to trades on a route level, and does not populate on a per security    |
|                          |  | basis.                                                                 |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_REMAIN_BALANCE``|  | ``FLOAT64`` ``ROUTE`` The EMSX Multi-Leg Remaining Balance is the      |
|                          |  | balance yet to be filled across the legs of a multi-leg strategy. This |
|                          |  | field is applicable to trades on a route level, and does not populate  |
|                          |  | on a per security basis.                                               |  
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_STRATEGY``      |  | ``STRING`` ``ROUTE`` The EMSX Multi-Leg Strategy Name is the name of   |
|                          |  | the multi-leg strategy for the route. This field is applicable to      |
|                          |  | trades on avroute level, and does not populate on a per security basis.| 
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_TOTAL_QUANTITY``|  | ``INT32`` ``ROUTE`` The EMSX Multi-Leg Quantity is the total number of |
|                          |  | mutli-leg packages in the order. One package consists of several legs  |
|                          |  | with individual quantities of certain options for each leg. This field |
|                          |  | is applicable to trades on a route level, and does not populate on a   |
|                          |  | per security basis.                                                    |
+--------------------------+---------------------------------------------------------------------------+ 


EMSX Element Definition (N to Z)
================================


+-------------------------------+----------------------------------------------------------------------+
|Field                          |Definition                                                            |
+===============================+======================================================================+
|``EMSX_NOTES``                 |  | ``STRING`` ``O,R`` The EMSX Instructions is the free form         |
|                               |  | instructions that may be sent to the broker. This field is        |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_NSE_AVG_PRICE``         |  | ``FLOAT64`` ``O,R`` The EMSX National Stock Exchange Average Price|
|                               |  | is the average price of the fills completed for the order or route|
|                               |  | on the National Stock Exchange (NSE). This field is applicable to |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_NSE_FILLED``            |  | ``INT32`` ``O,R`` The EMSX National Stock Exchange Filled is the  |
|                               |  | total quantity of the fills completed for the order or route on   |
|                               |  | the National Stock Exchange (NSE). This field is applicable to    |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORD_REF_ID``            |  | ``STRING`` ``ORDER`` The EMSX Order Reference ID. The element is  |
|                               |  | called the ``EMSX_ORDER_REF_ID`` in the request/response services.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORDER_AS_OF_DATE``      |  | ``INT32`` ``ORDER`` The order as of date in EMSX.                 |
+-------------------------------+----------------------------------------------------------------------+

+----------------------------------+-------------------------------------------------------------------+
|``EMSX_ORDER_AS_OF_TIME_MICROSEC``|  | ``FLOAT64`` ``ORDER`` The order as of time in microseconds.    |
+----------------------------------+-------------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORDER_TYPE``            |  | ``STRING`` ``O,R`` The order type in EMSX. (e.g. market, limit,   |
|                               |  | stop limit and etc.)                                              |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORIGINATE_TRADER``      |  | ``STRING`` ``ORDER`` The trader who routed the order. This field  |
|                               |  | is applicable to trades on an order level, and does not populate  |
|                               |  | on a per security basis.                                          |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORIGINATE_TRADER_FIRM`` |  | ``STRING`` ``STATIC`` ``ORDER`` The firm of the trader who routed |
|                               |  | the order. This field is applicable to trades on an order level   |
|                               |  | and does not populate on a per security basis.                    | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_OTC_FLAG``              |  | ``STRING`` ``ROUTE`` The OTC flag in EMSX.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_P_A``                   |  | ``STRING`` ``ROUTE`` The EMSX Principal/Agency element specifies  |
|                               |  | the capacity in which the broker acts for a particular order and  |
|                               |  | route; either 'P' - Principal or 'A' - Agency. This field is      |
|                               |  | applicable to trades on a route level, and does not populate on a |
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PERCENT_REMAIN``        |  | ``FLOAT64`` ``O,R`` The remaining balance of the order as a       |
|                               |  | percentage of the projected remaining volume in the day. This     |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PM_UUID``               |  | ``INT32`` ``STATIC`` ``ORDER`` The Portfolio Manager UUID in AIM. |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_MGR``              |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Portfolio Manager is the |
|                               |  | name of the portfolio manager in the AIM function. For standalone |
|                               |  | users, this is the same as the EMSX Trader Name. This field is    |
|                               |  | applicable to trades on an order level, and does not populate on a|
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_NAME``             |  | ``STRING`` ``ORDER`` The EMSX Portfolio Name is the name of the   |
|                               |  | portfolio from which the order is sourced. This field is          | 
|                               |  | applicable to trades on an order level, and does not populate on a|
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_NUM``              |  | ``INT32`` ``ORDER`` The EMSX Portfolio Number is the number of the|
|                               |  | portfolio from which the order is sourced. This field is          |
|                               |  | applicable to trades on an order level, and does not populate on a|
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_POSITION``              |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Position specifies if the| 
|                               |  | position for the order is open  or closed. This field is          |
|                               |  | applicable to trades on an order level, and does not populate on  |
|                               |  | a per security basis.                                             | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PRINCIPAL``             |  | ``FLOAT64`` ``O,R`` The EMSX Principal is the gross executed value| 
|                               |  | of the trade. This field is applicable to trades on an order      |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PRODUCT``               |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Product Name is the      |
|                               |  | product type of the order. This field is applicable to trades on  |
|                               |  | an order level, and does not populate on a per security basis.    |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_QUEUED_DATE``           |  | ``INT32`` ``O,R`` The EMSX Queued Date is the date in the future  |
|                               |  | when a route will be released to the broker. This field is        |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_QUEUED_TIME``           |  | ``INT32`` ``O,R`` The time in the future when a route will be     |
|                               |  | released to the broker. This field is applicable to trades on an  |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_QUEUED_TIME_MICROSEC``  |  | ``FLOAT64`` ``O,R`` ``EMSX_QUEUED_TIME`` in microseconds.         | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REASON_CODE``           |  | ``STRING`` ``O,R`` The reason code customized by a firm for the   |
|                               |  | order or route. The corresponding description for a code is in    |
|                               |  | ``EMSX``  Reason Code Description. This field is applicable to    |
|                               |  | trades on an order and/or route level, and does not populate on a | 
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REASON_DESC``           |  | ``STRING`` ``O,R`` The EMSX Reason Code Description is the reason |
|                               |  | description customized by a firm for the order or route. The      |
|                               |  | corresponding code for the description is in EMSX Reason Code.    |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  |  and does not populate on a per security basis.                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REMAIN_BALANCE``        |  | ``FLOAT64`` ``O,R`` The amount of shares not executed on and still|
|                               |  | outstanding. This field is applicable to trades on an order       |
|                               |  | and/or route level, and does not populate on a per security basis.| 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_AS_OF_DATE``      |  | ``INT32`` ``ROUTE`` The date of the creation of the route in the  |
|                               |  | user's time zone. This field is applicable to trades on a route   |
|                               |  | level, and does not populate on a per security basis.             |
+-------------------------------+----------------------------------------------------------------------+

+----------------------------------+-------------------------------------------------------------------+
|``EMSX_ROUTE_AS_OF_TIME_MICROSEC``|  | ``FLOAT64`` ``ROUTE`` The route as of time in microseconds.    |
+----------------------------------+-------------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_CREATE_DATE``     |  | ``INT32`` ``STATIC`` ``ROUTE`` The date of the creation of the    |
|                               |  | route in the user's time zone. This field is applicable to trades |
|                               |  | on a route level, and does not populate on a per security basis.  |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_CREATE_TIME``     |  | ``INT32`` ``STATIC`` ``ROUTE`` The time of the creation of the    |
|                               |  | route in seconds from midnight in the user's time zone. This field| 
|                               |  | is applicable to trades on a route level, and does not populate on| 
|                               |  | a per security basis.                                             |
+-------------------------------+----------------------------------------------------------------------+

+-----------------------------------+------------------------------------------------------------------+
|``EMSX_ROUTE_CREATE_TIME_MICROSEC``|  | ``FLOAT64`` ``STATIC`` ``ROUTE`` ``EMSX_ROUTE_CREATE_TIME``   |
|                                   |  | in microseconds.                                              |
+-----------------------------------+------------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_ID``              |  | ``INT32`` ``STATIC`` ``O,R`` The transaction number of the route  |
|                               |  | in the system. This field is applicable to trades on an order     |
|                               |  | and/or route level, and does not populate on a per security basis.| 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_LAST_UPDATE_TIME``|  | ``INT32`` ``ROUTE`` The time stamp of the last execution or       |
|                               |  | cancellation on a route. This field is applicable to trades on a  |
|                               |  | route level and does not populate on a per security basis.        |
+-------------------------------+----------------------------------------------------------------------+

+----------------------------------------+-------------------------------------------------------------+
|``EMSX_ROUTE_LAST_UPDATE_TIME_MICROSEC``|  | ``FLOAT64`` ``ROUTE`` ``EMSX_ROUTE_LAST_UPDATE_TIME`` in |
|                                        |  | microseconds.                                            |
+----------------------------------------+-------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_PRICE``           |  | ``FLOAT64`` ``O,R`` The route price benchmark for the route. This |
|                               |  | is the midpoint during market hours, and the next opening price   |
|                               |  | between exchange sessions. This field is applicable to trades on  |
|                               |  | an order and/or route level, and does not populate on a per       |
|                               |  | security basis.                                                   |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_REF_ID``          |  | ``STRING`` ``ROUTE`` The EMSX Route Reference ID. The element is  |
|                               |  | called the ``EMSX_ROUTE_REF_ID`` in the request/response services.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEC_NAME``              |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Security Name is the long| 
|                               |  | name of the security being traded in EMSX. This field is          |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEDOL``                 |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Stock Exchange Daily     |
|                               |  | Official List - SEDOL (Stock Exchange Daily Official List) number |
|                               |  | of the security in the order. This field is applicable to trades  |
|                               |  | on an order level and does not populate on a per security basis.  | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEQUENCE``              |  | ``INT32`` ``STATIC`` ``O,R`` The sequence number generated by the |
|                               |  | EMSX function for the order. This field is applicable to trades on| 
|                               |  | an order and/or route level,and does not populate on a per        |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SETTLE_AMOUNT``         |  | ``FLOAT64`` ``O,R`` The EMSX Net Money is the executed value of   |
|                               |  | trade net of commission, taxes, and fees. This field is applicable| 
|                               |  | to trades on an order and/or route level, and does not populate on| 
|                               |  | a per security basis.                                             |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SETTLE_DATE``           |  | ``INT32`` ``O,R`` The date on which payment is due to settle the  |
|                               |  | trade for the order or route. This field is applicable to trades  |
|                               |  | on an order and/or route level, and does not populate on a per    |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SI``                    |  | ``STRING`` ``ORDER`` The Systematic Internalizer in MiFID II.     |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SIDE``                  |  | ``STRING`` ``STATIC`` ``ORDER`` The EMSX Side specifies whether   |
|                               |  | the order or route is generated from the buy side (B) or sell side|
|                               |  | (S). This field is applicable to trades on an order and/or route  |
|                               |  | level, and does not populate on a per security  basis.            |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_START_AMOUNT``          |  | ``INT32`` ``STATIC`` ``ORDER`` The original order quantity at     |
|                               |  | creation of the order. This field is applicable to trades on an   |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STATUS``                |  | ``STRING`` ``O,R`` The current status of the order or route. This |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STEP_OUT_BROKER``       |  | ``STRING`` ``ORDER`` The name of the broker the executing broker  |
|                               |  | gives all or a portion of the commission to for the order. This   |
|                               |  | field is applicable to trades on an order level, and does not     |
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STOP_PRICE``            |  | ``FLOAT64`` ``O,R`` The price at which an order to buy or sell    |
|                               |  | a security is triggered. Once the trigger price is reached, the   |
|                               |  | order becomes a market order. This field is applicable to trades  |
|                               |  | on an order and/or route level, and does not populate on a per    |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_END_TIME``     |  | ``INT32`` ``O,R`` The end time for the EMSX Strategy Type         |
|                               |  | ``EMSX_STRATEGY_TYPE``. This field is applicable to trades on an  |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_PART_RATE1``   |  | ``FLOAT64`` ``O,R`` The first participation rate for the          |
|                               |  | algorithmic strategy on the route. This field is applicable to    |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_PART_RATE2``   |  | ``FLOAT64`` ``O,R`` The second participation rate for the         |
|                               |  | algorithmic strategy on the  route. This field is applicable to   |
|                               |  | trades on an order and/or route level, and does not populate on a | 
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_STRATEGY_START_TIME``   |  | ``INT32`` ``O,R`` The start time for the EMSX Strategy Type       |
|                               |  | ``EMSX_STRATEGY_TYPE``. This field is applicable to trades on an  |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_STYLE``        |  | ``STRING`` ``O,R`` The execution urgency for the algorithmic      |
|                               |  | strategy on the route; values are customized by individual        |
|                               |  | brokers. This field is applicable to trades on an order and/or    |
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_TYPE``         |  | ``STRING`` ``O,R`` The method used for the route or order,        |
|                               |  | customized by individual brokers. This field is applicable to     |
|                               |  | trades on an order and/or  route level, and does not populate on a|  
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TICKER``                |  | ``STRING`` ``STATIC`` ``ORDER`` The ticker specifies the          |
|                               |  |  abbreviation assigned to a security for trading purposes. This   |
|                               |  | field is applicable to trades on an order level, and does not     |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TIF``                   |  | ``STRING`` ``O,R`` The time limit of the order; how long the order|
|                               |  | remains in effect for. This field is applicable to trades on an   |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TIME_STAMP``            |  | ``INT32`` ``O,R`` The time the order or route is created, in      |
|                               |  | seconds from midnight based on the user's time. This field is     |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 |  
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TIME_STAMP_MICROSEC``   |  | ``FLOAT64`` ``O,R`` ``EMSX_TIME_STAMP`` in microseconds.          |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRAD_UUID``             |  | ``INT32`` ``ORDER`` The UUID of the EMSX Trader. This field is    |
|                               |  | equivalent to ``EMSX_TRADER_UUID`` in the elements in the         |
|                               |  | request/response.                                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADE_DESK``            |  | ``STRING`` `` STATIC`` ``ORDER`` The name of the trading desk on  |
|                               |  | the order. This field is applicable to trades on an order level,  |
|                               |  | and does not populate on a per security basis. This is            |
|                               |  | specifically for AIM.                                             | 
+-------------------------------+----------------------------------------------------------------------+

+----------------------------------+-------------------------------------------------------------------+
|``EMSX_TRADE_REPORTING_INDICATOR``|  | ``STRING`` ``STATIC`` ``ORDER`` The trade reporting indicator  |
|                                  |  | for MiFID II.                                                  |
+----------------------------------+-------------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADER``                |  | ``STRING`` ``ORDER`` The current trader's Bloomberg login name.   |
|                               |  | This field is to trades on an order level, and does not populate  |
|                               |  | on a per security basis.                                          |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADER_NOTES``          |  | ``STRING`` ``ORDER`` The free form notes for the trader which are |
|                               |  | not passed on to the brokers. This field is applicable to trades  |
|                               |  | on an order level, and does not populate on a per security basis. | 
+-------------------------------+----------------------------------------------------------------------+

+----------------------------------+-------------------------------------------------------------------+
|``EMSX_TRANSACTION_REPORTING_MIC``|  | ``STRING`` ``ROUTE`` The transaction reporting MIC code in     |
|                                  |  | MiFID II.                                                      |
+----------------------------------+-------------------------------------------------------------------+

+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TS_ORDNUM``             |  | ``INT32`` ``STATIC`` ``ORDER`` The order number generated by the  | 
|                               |  | AIM. For a non-AIM user, this number is the same as the           |
|                               |  | ``EMSX_SEQUENCE`` Number. This field is applicable to trades on   |
|                               |  | an order level, and does not populate on a per security basis.    |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TYPE``                  |  | ``STRING`` ``O,R`` The type of the order; this can be a           |
|                               |  | preconfigured valued or a value configured by the individual      |
|                               |  | broker. This field is applicable to trades on an order and/or     |
|                               |  | route level, and does not populate on a per security basis.       |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_UNDERLYING_TICKER``     |  | ``STRING`` ``STATIC`` ``ORDER`` The instrument to which a         |
|                               |  | derivative, such as an equity or index option, is related. This   |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_URGENCY_LEVEL``         |  | ``INT32`` ``ROUTE`` The integer which is the parameter for a      |
|                               |  | route strategy, which determines a route's priority. This field is|
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_COMM_AMOUNT``      |  | ``FLOAT64`` ``O,R`` The EMSX User Commission Amount is the total  |
|                               |  | commission charged on the trade based on user-defined commission  |
|                               |  | rates entered. This field is applicable to trades on an order     |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_COMM_RATE``        |  | ``FLOAT64`` ``O,R`` The EMSX User Commission Rate is the          |
|                               |  | user-defined commission rate for the trade. This field is         |
|                               |  | applicable to trades on an order and/or route level, and does not | 
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_FEES``             |  | ``FLOAT64`` ``O,R`` The user-defined fees for the trade. This     |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_USER_NET_MONEY``        |  | ``FLOAT64`` ``O,R`` The executed value of trade net of            |
|                               |  | user-defined commission, taxes, and fees. This field is applicable|
|                               |  | to trades on an order and/or route level, and does not populate on|
|                               |  | a per security basis.                                             |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_WAIVER_FLAG``           |  | ``STRING`` ``ROUTE`` The waiver flag indicator for MiFID II.      |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_WORK_PRICE``            |  | ``FLOAT64`` ``ORDER`` The limit price of the last working route of|
|                               |  | a given order. This field is applicable to trades on an order     |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_WORKING``               |  | ``INT32`` ``O,R`` The amount the broker is working with. This     |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_YELLOW_KEY``            |  | ``STRING`` ``STATIC`` ``ORDER`` The yellow key of the security in |
|                               |  | the order. This is applicable to trades on an order level, and    |
|                               |  | does not populate on a per security basis.                        | 
+-------------------------------+----------------------------------------------------------------------+
|``EVENT_STATUS``               |  | ``INT32`` Special field to indicate the status type of an event.  |
|                               |  | This is a means of determining the type of event you have         |
|                               |  | received. This helps the developers to know what structure of the |
|                               |  | message should be, including the expected fields that should be   |
|                               |  | available. (e.g. ``EVENT_STATUS = 1`` Heartbeat Message,          |                                                 
|                               |  | ``EVENT_STATUS = 6`` new order or route messsags on all           |
|                               |  | subscription fields.)                                             |
+-------------------------------+----------------------------------------------------------------------+
|``MSG_SUB_TYPE``               |  | ``STRING`` Special field to indicate the service specific details |  
|                               |  | in the EMSX API. ``MSG_SUB_TYPE = O`` is to indicate an Order     |
|                               |  | event and ``MSG_SUB_TYPE = R`` is to indicate a Route event.      |
+-------------------------------+----------------------------------------------------------------------+
|``MSG_TYPE``                   |  | ``STRING`` Special field to indicate the service specific details.|
|                               |  | The value is always ``MSG_TYPE = E`` for EMSX message type.       |
+-------------------------------+----------------------------------------------------------------------+