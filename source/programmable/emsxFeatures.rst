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

	//blp/emapisvc_beta/order; **team=my_team_name** ?fields=EMSX_ASSIGNED_TRADER, EMSX_BASKET_NAME, EMSX_CFD_FLAG, EMSX_AMOUNT


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

+-----------------------------+------------------------------------------------------------------------+
|Field                        |Definition                                                              |
+=============================+========================================================================+
|``EMSX_ACCOUNT``             |  | The account of the routing firm as designated by the broker chosen. |
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_AMOUNT``              |  | The total amount of the order or route. This field is applicable to |
|                             |  | trades on an order and/or route level, and does not populate on a   |
|                             |  | per security basis.                                                 | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ARRIVAL_PRICE``       |  | The arrival price benchmark for order.The arrival price is the      | 
|                             |  | midpoint during market hours, and the next opening price between    |
|                             |  | exchange sessions. This field is applicable to trades on an order   |
|                             |  | and/or route level, and does not populate on a per security basis.  |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ASSET_CLASS``         |  | The asset class of the order. This field is applicable to trades on |
|                             |  | an order and/or route level, and does not populate on a per security|
|                             |  | basis.                                                              | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ASSIGNED_TRADER``     |  | The name of the trader assigned to the order. This field is         |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_AVG_PRICE``           |  | The average price for one share executed with the                   |
|                             |  | order, calculated over the life of the order. This field is         |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BASKET_NAME``         |  | The name assigned to a group of related orders contained in a       |
|                             |  | basket. This field is applicable to trades on an order and/or route |
|                             |  | level, and does not populate on a per security basis.               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BASKET_NUM``          |  | The number corresponding to the ``EMSX_BASKET_NAME`` assigned to a  |
|                             |  | group of related orders. This field is applicable to trades on an   |
|                             |  | order and/or route level, and does not populate on a per security   |
|                             |  | basis.                                                              | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER``              |  | The code for the broker with whom the order is routed. This field   |
|                             |  | is applicable to trades on an order and/or route level, and does    |
|                             |  | not populate on a per security basis.                               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BROKER_COMM``         |  | The amount of commission charged by the broker for the order or     |
|                             |  | route. This field is applicable to trades on an order and/or route  |
|                             |  | level, and does not populate on a per security basis.               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BSE_AVG_PRICE``       |  | The EMSX Bombay Stock Exchange Average Price. Average price of the  |
|                             |  | fills completed for the order or route on the Bombay Stock Exchange |
|                             |  | (BSE). This field is applicable to trades on an order and/or route  |
|                             |  | level, and does not populate on a per security basis.               | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_BSE_FILLED``          |  | The EMSX Bombay Stock Exchange Filled.  Total quantity of the fills |
|                             |  | completed for the order or route on the Bombay Stock Exchange (BSE).|
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CFD_FLAG``            |  | The EMSX Contract For Difference Flag. Indicates if the order is a  |
|                             |  | contract for differences (CFD) trade. This field is applicable to   |
|                             |  | trades on an order and/or route level, and does not populate on a   |
|                             |  | per security basis.                                                 |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CLEARING_ACCOUNT``    |  | The clearing account defined on a futures or option route. This     |
|                             |  | field is applicable to trades on an order and/or route level, and   |
|                             |  | does not populate on a per security basis.                          | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CLEARING_FIRM``       |  | The clearing firm defined on a futures or options route. This field |
|                             |  | is applicable to trades on an order and/or route level, and does    |
|                             |  | not populate on a per security basis.                               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_COMM_DIFF_FLAG``      |  | The EMSX Commission Difference between broker commission and AIM    |
|                             |  | (Asset and Investment Manager) commission values. This field is     |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_COMM_RATE``           |  | The EMSX Commission Rate of commission charged on the trade. This   |
|                             |  | field is applicable to trades on an order and/or route level, and   |
|                             |  | does not populate on a per security basis.                          | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CURRENCY_PAIR``       |  | The EMSX Currency Pair which provides the spot rate to convert the  |
|                             |  | security's currency and the user's currency. This field is          |
|                             |  | applicable to trades on an order and/or route level, and does not   | 
|                             |  | populate on a per security basis.                                   |  
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_CUSTOM_ACCOUNT``      |  | The EMSX Route Account, is the account value at the level of the    |
|                             |  | route. This field is applicable to trades on an order and/or route  |
|                             |  | level, and does not populate on a per security basis.               | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DATE``                |  | The EMSX Order Creation Date is the date on which the order is      |
|                             |  | created. This field is applicable to trades on an order and/or      |
|                             |  | route level, and does not populate on a per security basis.         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DAY_AVG_PRICE``       |  | The EMSX Day Average Price is the average price for one share       |
|                             |  | executed with the order, based on shares filled today. This field is|
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DAY_FILL``            |  | The EMSX Day Fill is the total quantity of shares filled today for  |
|                             |  | this order/security, across any number of brokers. This field is    |
|                             |  | applicable to trades on an order and/or route level, and does not   |
|                             |  | populate on a per security basis.                                   | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_DIR_BROKER_FLAG``     |  | The EMSX Directed Brokers is an indicator of whether the order has  |
|                             |  | funds with the directed brokers defined. This field is applicable to|
|                             |  | trades on an order and/or route level, and does not populate on a   |
|                             |  | per security basis.                                                 |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXCHANGE``            |  | The EMSX Exchange is the exchange code for the where the security in|
|                             |  | the order is listed. This field is applicable to trades on an order | 
|                             |  | and/or route level, and does not populate on a per security basis.  | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXCHANGE_DESTINATION``|  | The EMSX Exchange Destination is the Exchange destination of the    |
|                             |  | security for the order or route. This field is applicable to trades |
|                             |  | on an order and/or route level, and does not populate on a per      |
|                             |  | security basis.                                                     |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_EXECUTE_BROKER``      |  | The EMSX Execution Broker is the executing broker on the trade for  |
|                             |  | the route. This field is applicable to trades on an order and/or    |
|                             |  | route level, and does not populate on a per security basis.         | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_FILLED``              |  | The quantity of shares which have been executed by broker. This     |
|                             |  | field is applicable to trades on an order and/or route level, and   | 
|                             |  | does not populate on a per security basis.                          | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_FILL_ID``             |  | The fill number associated with a route. This field is applicable   |
|                             |  | to trades on an order and/or route level, and does not populate     |
|                             |  | on a per security basis.                                            | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_GTD_DATE``            |  | The EMSX Good to Date is the date the order is in force until, based|
|                             |  | on local exchange date and time. This field is applicable to trades |
|                             |  | on an order and/or route level, and does not populate on a per      |
|                             |  | security basis.                                                     | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_HAND_INSTRUCTION``    |  | The EMSX Handling Instruction is the instructions for handling the  |
|                             |  | order or route. The values can be preconfigured or a value          |
|                             |  | customized by the broker. This field is applicable to trades on an  |
|                             |  | order and/or route level, and does not populate on a per security   |
|                             |  | basis.                                                              |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_IDLE_AMOUNT``         |  | The quantity of shares yet to be routed or executed, equal to the   |
|                             |  | order quantity minus amounts filled, unreleased, and routed. This   |
|                             |  | field is applicable to trades on an order and/or route level, and   |
|                             |  | does not populate on a per security basis.                          |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_INVESTOR_ID``         |  | The identifier for the buy side investor as used for markets such as|
|                             |  | Korea and Taiwan. This field is applicable to trades on an order    |
|                             |  | and/or route level, and does not populate on a per security basis.  | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_ISIN``                |  | The EMSX International Securities Identification Number os the ISIN |
|                             |  | (International Securities Identification Number) of the security in |
|                             |  | the order. This field is applicable to trades on an order and/or    |
|                             |  | route level, and does not populate on a per security basis.         |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_IS_MANUAL_ROUTE``     |  | The EMSX Manual Route indicates that the route was not communicated |
|                             |  | electronically to the broker. This field is applicable to trades on |
|                             |  | an order and/or route level, and does not populate on a per security|
|                             |  | basis.                                                              | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_FILL_DATE``      |  | The date of the last fill based on the user's time zone. This field |
|                             |  | is applicable to trades on an order and/or route level, and does    |
|                             |  | not populate on a per security basis.                               | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_MAREKT``         |  | The last market of execution for a trade as returned by the broker. |
|                             |  | This field is applicable to trades on an order and/or route level,  |
|                             |  | and does not populate on a per security basis.                      |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_PRICE``          |  | The last execution price for a trade. This field is applicable to   |
|                             |  | trades on an order and/or route level, and does not populate on a   |
|                             |  | per security basis.                                                 |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_SHARES``         |  | The last executed quantity for a trade. This field is applicable to |
|                             |  | trades on an order and/or route level, and does not populate on a   |
|                             |  | per security basis.                                                 |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LAST_FILL_TIME``      |  | The time of the last fill based on seconds from midnight in the     |
|                             |  | user's time zone. This field is applicable to trades on an order    |
|                             |  | and/or route level, and does not populate on a per security basis.  | 
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_LIMIT_PRICE``         |  | The price which is the maximum the order to buy securities or       |
|                             |  | commodities should be executed at; or the minimum at which          |
|                             |  | securities or commodities should be sold. This field is applicable  |
|                             |  | to trades on an order and/or route level, and does not populate on  |
|                             |  | a per security basis.                                               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_MISC_FEES``           |  | The EMSX Miscellaneous Fees is the assorted fees associated with a  |
|                             |  | trade, such as regulatory fees and taxes. This field is applicable  | 
|                             |  | to trades on an order and/or route level, and does not populate on  |
|                             |  | a per security basis.                                               |
+-----------------------------+------------------------------------------------------------------------+
|``EMSX_MOD_PEND_STATUS``     |  | Only valid for Sell-Side EMSX on E2E (EMSX to EMSX) settings.       |
|                             |  | Fields that can populate: Size, Price, Stop, GTDDate, TIF, Type and |
|                             |  | instruments.                                                        |
|                             |  | e.g. EMSX_MOD_PEND_STATUS= "Pending Info|Size: 500.0 -> 200.0|      |
|                             |  | Price 2.0000 -> 4.0000|Instr: -> test instr"                        |
+-----------------------------+------------------------------------------------------------------------+


Multi-Leg Element Definition
============================


+--------------------------+---------------------------------------------------------------------------+
|Field                     |Definition                                                                 |
+==========================+===========================================================================+
|``EMSX_ML_LEG_QUANTITY``  |  | The EMSX Multi-Leg Shares per Leg is the number of shares per leg in   |
|                          |  | the multi-leg strategy. This field is applicable to trades on an order |
|                          |  | and/or route level, and does not populate on a per security basis.     |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_NUM_LEGS``      |  | The EMSX Multi-Leg Number Legs is the number of legs in the multi-leg  |
|                          |  | strategy. This field is applicable to trades on an order and/or route  |
|                          |  | level, and does not populate on a per security basis.                  |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_PERCENT_FILLED``|  | The EMSX Multi-Leg Percent Filled is the percent of legs filled in a   |
|                          |  | multi-leg strategy. This field is applicable to trades on an order     |
|                          |  | and/or route level, and does not populate on a per security basis.     |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_RATIO``         |  | The EMSX Multi-Leg Ratio is the factor that controls the number of     |
|                          |  | securities in each leg. This field is applicable to trades on an order |
|                          |  | and/or route level, and does not populate on a per security basis.     |
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_REMAIN_BALANCE``|  | The EMSX Multi-Leg Remaining Balance is the balance yet to be filled   |
|                          |  | across the legs of a multi-leg strategy. This field is applicable to   |
|                          |  | trades on an order and/or route level, and does not populate on a per  |
|                          |  | security basis.                                                        |  
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_STRATEGY``      |  | The EMSX Multi-Leg Strategy Name is the name of the multi-leg strategy |
|                          |  | for the route. This field is applicable to trades on an order and/or   |
|                          |  | route level, and does not populate on a per security basis.            | 
+--------------------------+---------------------------------------------------------------------------+
|``EMSX_ML_TOTAL_QUANTITY``|  | The EMSX Multi-Leg Quantity is the total number of mutli-leg packages  |
|                          |  | in the order. One package consists of several legs with individual     |
|                          |  | quantities of certain options for each leg. This field is applicable   |
|                          |  | to trades on an order and/or route level, and does not populate on a   |
|                          |  | per security basis.                                                    |
+--------------------------+---------------------------------------------------------------------------+ 


EMSX Element Definition (N to Z)
================================


+-------------------------------+----------------------------------------------------------------------+
|Field                          |Definition                                                            |
+===============================+======================================================================+
|``EMSX_NOTES``                 |  | The EMSX Instructions is the free form instructions that may be   |
|                               |  | sent to the broker. This field is applicable to trades on an order|
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_NSE_AVG_PRICE``         |  | The EMSX National Stock Exchange Average Price is the average     |
|                               |  | price of the fills completed for the order or route on the        |
|                               |  | National Stock Exchange (NSE). This field is applicable to trades |
|                               |  | on an order and/or route level, and does not populate on a per    |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_NSE_FILLED``            |  | The EMSX National Stock Exchange Filled is the total quantity of  |
|                               |  | the fills completed for the order or route on the National Stock  |
|                               |  | Exchange (NSE). This field is applicable to trades on an order    |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORIGINATE_TRADER_FIRM`` |  | The firm of the trader who routed the order. This field is        |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ORIGINATE_TRADER``      |  | The name of the trader who routed the order. This field is        |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_P_A``                   |  | The EMSX Principal/Agency element specifies the capacity in which |
|                               |  | the broker acts for a particular order and route; either          |
|                               |  | 'P' - Principal or 'A' - Agency. This field is applicable to      |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PERCENT_REMAIN``        |  | The remaining balance of the order as a percentage of the         |
|                               |  | projected remaining volume in the day. This field is applicable   |
|                               |  | to trades on an order and/or route level, and does not populate   |
|                               |  | on a per security basis.                                          |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_MGR``              |  | The EMSX Portfolio Manager is the name of the portfolio manager   |
|                               |  | in the AIM function. For standalone users, this is the same as the|
|                               |  | EMSX Trader Name. This field is applicable to trades on an order  |
|                               |  | and/or on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_NAME``             |  | The EMSX Portfolio Name is the name of the portfolio from which   |
|                               |  | the order is sourced. This field is applicable to trades on an    | 
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PORT_NUM``              |  | The EMSX Portfolio Number is the number of the portfolio from     |
|                               |  | which the order is sourced. This field is applicable to trades on |
|                               |  | an order and/or route level, and does not populate on a per       |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_POSITION``              |  | The EMSX Position specifies if the position for the order is open |
|                               |  | or closed. This field is applicable to trades on an order and/or  |
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PRINCIPAL``             |  | The EMSX Principal is the gross executed value of the trade. This |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_PRODUCT``               |  | The EMSX Product Name is the product type of the order. This field|
|                               |  | is applicable to trades on an order and/or route level, and does  |
|                               |  | not populate on a per security basis.                             |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_QUEUED_DATE``           |  | The EMSX Queued Date is the date in the future when a route will  |
|                               |  | be released to the broker. This field is applicable to trades on  |
|                               |  | an order and/or route level, and does not populate on a per       |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_QUEUED_TIME``           |  | The time in the future when a route will be released to the broker|
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REASON_CODE``           |  | The reason code customized by a firm for the order or route. The  |
|                               |  | corresponding description for a code is in EMSX  Reacon Code      |
|                               |  | Description. This field is applicable to trades on an order and/or|
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REASON_DESC``           |  | The EMSX Reason Code Description is the reason description        |
|                               |  | customized by a firm for the order or route. The corresponding    |
|                               |  | code for the description is in EMSX Reason Code. This  field is   |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_REMAIN_BALANCE``        |  | The amount of shares not executed on and still outstanding. This  |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_CREATE_DATE``     |  | The date of the creation of the route in the user's time zone.    |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_ROUTE_CREATE_TIME``     |  | The time of the creation of the route in seconds from midnight in |
|                               |  | the user's time zone. This field is applicable to trades on an    |
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_ROUTE_ID``              |  | The transaction number of the route in the system. This field is  |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_LAST_UPDATE_TIME``|  | The time stamp of the last execution or cancellation on a route.  |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_ROUTE_PRICE``           |  | The route price benchmark for the route. This is the midpoint     |
|                               |  | during market hours, and the next opening price between exchange  |
|                               |  | sessions. This field is applicable to trades on an order and/or   |
|                               |  | route level, and does not populate on a per security basis.       |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEC_NAME``              |  | The EMSX Security Name is the long name of the security being     |
|                               |  | traded in EMSX. This field is applicable to trades on an order    |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEDOL``                 |  | The EMSX Stock Exchange Daily Official List - SEDOL (Stock        |
|                               |  | Exchange Daily Official List) number of the security in the order.|
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SEQUENCE``              |  | The sequence number generated by the EMSX function for the order. |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SETTLE_AMOUNT``         |  | The EMSX Net Money is the executed value of trade net of          |
|                               |  | commission, taxes, and fees. This field is applicable to trades on|
|                               |  | an order and/or route level, and does not populate on a per       |
|                               |  | security basis.                                                   |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SETTLE_DATE``           |  | The date on which payment is due to settle the trade for the order|
|                               |  | or route. This field is applicable to trades on an order and/or   |
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_SIDE``                  |  | The EMSX Side specifies whether the order or route is generated   |
|                               |  | from the buy side (B) or sell side (S). This field is applicable  |
|                               |  | to trades on an order and/or route level, and does not populate   |
|                               |  | on a per security  basis.                                         |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_START_AMOUNT``          |  | The original order quantity at creation of the order. This field  |
|                               |  | is applicable to trades on an order and/or route level, and does  |
|                               |  | not populate on a per security basis.                             |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STATUS``                |  | The current status of the order or route. This field is applicable|
|                               |  | to trades on an order and/or route level, and does not populate   |
|                               |  | on a per security basis.                                          | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STEP_OUT_BROKER``       |  | The name of the broker the executing broker gives all or a portion|
|                               |  | of the commission to for the order. This field is applicable to   |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STOP_PRICE``            |  | The price at which an order to buy or sell a security is          |
|                               |  | triggered. Once the trigger price is reached, the order becomes   |
|                               |  | a market order. This field is applicable to trades on an order    |
|                               |  | and/or route level, and does not populate on a per security basis.| 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_END_TIME``     |  | The end time for the EMSX Strategy Type ``EMSX_STRATEGY_TYPE``.   |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_PART_RATE1``   |  | The first participation rate for the algorithmic strategy on the  |
|                               |  | route. This field is applicable to trades on an order and/or route|
|                               |  | level, and does not populate on a per security basis.             | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_PART_RATE2``   |  | The second participation rate for the algorithmic strategy on the |
|                               |  | route. This field is applicable to trades on an order and/or route|
|                               |  | level, and does not populate on a per security basis.             |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_STRATEGY_START_TIME``   |  | The tart time for the EMSX Strategy Type ``EMSX_STRATEGY_TYPE``.  |
|                               |  | This field is applicable to trades on an order and/or route level,|
|                               |  | and does not populate on a per security basis.                    |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_STYLE``        |  | The execution urgency for the algorithmic strategy on the route;  |
|                               |  | values are customized by individual brokers. This field is        |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_STRATEGY_TYPE``         |  | The method used for the route or order, customized by individual  |
|                               |  | brokers. This field is applicable to trades on an order and/or    |
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TICKER``                |  | The ticker specifies the abbreviation assigned to a security for  |
|                               |  | trading purposes. This field is applicable to trades on an order  |
|                               |  | and/or route level, and does not populate on a per security basis.| 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TIF``                   |  | The time limit of the order; how long the order remains in effect |
|                               |  | for. This field is applicable to trades on an order and/or route  |
|                               |  | level, and does not populate on a per security basis.             |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TIME_STAMP``            |  | The time the order or route is created, in seconds from midnight  |
|                               |  | based on the user's time. This field is applicable to trades on an|
|                               |  | order and/or route level, and does not populate on a per security |
|                               |  | basis.                                                            |  
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADE_DESK``            |  | The name of the trading desk on the order. This field is          |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADER``                |  | The current trader's Bloomberg login name. This field is          |
|                               |  | to trades on an order and/or route level, and does not populate   |
|                               |  | on a per security basis.                                          |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TRADER_NOTE``           |  | The ree form notes for the trader which are not passed on to the  |
|                               |  | brokers. This field is applicable to trades on an order and/or    |
|                               |  | route level, and does not populate on a per security basis.       | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TS_ORDNUM``             |  | The order number generated by the AIM function. For a non-AIM     |
|                               |  | user, this number is the same as the EMSX Sequence Number. This   |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_TYPE``                  |  | The type of the order; this can be a preconfigured valued or a    |
|                               |  | value configured by the individual broker. This field is          |
|                               |  | applicable to trades on an order and/or route level, and does not |
|                               |  | populate on a per security basis.                                 |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_UNDERLYING_TICKER``     |  | The instrument to which a derivative, such as an equity or index  |
|                               |  | option, is related. This field is applicable to trades on an order|
|                               |  | and/or route level, and does not populate on a per security basis.| 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_URGENCY_LEVEL``         |  | The integer which is the parameter for a route strategy, which    |
|                               |  | determines a route's priority. This field is applicable to trades |
|                               |  | on an order and/or route level, and does not populate on a per    |
|                               |  | security basis.                                                   | 
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_COMM_AMOUNT``      |  | The EMSX User Commission Amount is the total commission charged   |
|                               |  | on the trade based on user-defined commission rates entered. This |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_COMM_RATE``        |  | The EMSX User Commission Rate is the user-defined commission rate |
|                               |  | for the trade. This field is applicable to trades on an order     |
|                               |  | and/or route level, and does not populate on a per security basis.|
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_USER_FEES``             |  | The user-defined fees for the trade. This field is applicable to  |
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+ 
|``EMSX_USER_NET_MONEY``        |  | The executed value of trade net of user-defined commission, taxes,|
|                               |  | and fees. This field is applicable to trades on an order and/or   |
|                               |  | route level, and does not populate on a per security basis.       |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_WORKING``               |  | The amount the broker is working with. This field is applicable to|
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_WORK_PRICE``            |  | The limit price of the last working route of a given order. This  |
|                               |  | field is applicable to trades on an order and/or route level, and |
|                               |  | does not populate on a per security basis.                        |
+-------------------------------+----------------------------------------------------------------------+
|``EMSX_YELLOW_KEY``            |  | The yellow key of the security in the order. This is applicable to|
|                               |  | trades on an order and/or route level, and does not populate on a |
|                               |  | per security basis.                                               | 
+-------------------------------+----------------------------------------------------------------------+
