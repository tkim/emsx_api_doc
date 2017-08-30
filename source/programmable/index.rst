#####################
Programmable EMSX API
#####################

The programmable API provides developers with access to EMSX data via a number of programming languages. It can be used independantly of the EMSX Excel add-in, or as a complement. The API provide the developer with the means to replicate most of the behaviour available from the EMSX<GO> in the terminal.

The API supports two distinct programming paradigms; Subscription and Request/Response. Anyone already familiar with the Bloomberg API will recognize this approach. The EMSX API is simply an additional service (``//blp/emapisvc`` or ``//blp/emapisvc_beta``) on the Bloomberg API, with certain subtle differences due to the nature of the data involved.

The **Request/Response** methods are used to directly affect the state of the order book. Using these methods, the developer can Create and Delete (or Cancel) orders and routes (placements). When a request is made, for example ``CreateOrder``, the application must supply the necessary field values as parameters. The application must then wait for, and process, any responses (success or failure, for example) before the order or route can be futher utilized. Requests are matched to their responses through the use of CorrelationIDs.

The **subscription** service is used to maintain a local view of a user’s order book. Subscriptions are made for either orders or routes (placements), and any number of subscriptions can be made. The subscription is made at a user level, meaning all orders (or routes) for a given user are monitored on single subscription. 
When implementing subscription service, it’s important to write the code using two separate ``.subscribe()`` events for the order and route subscriptions. 

When a subscription is first made, the application will receive all the necessary messages to bring the local image of the user’s EMSX order book up to date. These initial messages will contain all the relevant fields for each order, both static and dynamic. Thereafter (within the same session), the user will only receive dynamic fields in any update messages. It is the developer’s responsibility to identify the changes, and respond appropriately. These messages are not stateful, and the API does not guarentee the order in which messages are received. However, this should not negatively impact the application, as long as the developer is aware of this and takes it into account. 

For example, when a ``CreateOrder`` is issued, as discussed above, it is perfectly feasible for a subscription event to be received before the response to the request. As this is a new order, the ``EMSX_SEQUENCE`` (the order ID number) will not yet be known on the client side. Therefore, you may be receiving messages for a sequence number that is not recognised, and will not be known until the response to the original ``CreateOrder`` request is processed. This can be dealt with through simple buffering of the subscription events.  In order to simplify this process the user has an option of using ``EMSX_ORD_REF_ID`` in subscription by supplying the ``EMSX_ORDER_REF_ID`` in your Request. This will allow the user to use the subscription event without having to wait for the response. The user can match requests with responses as well as subscription events. ``EMSX_ORDER_REF_ID`` has 16 character limitations but otherwise should be good to use as custom user defined field.

The ``EMSX_REQUEST_SEQ`` should also be added to every request. The ``EMSX_REQUEST_SEQ`` should consist of 64-bit integer and should be reset once a week.  The purpose of this unique user assigned sequence number is to prevent duplicate requests from being sent during system outages. The number also should be unique per serial number of the Bloomberg terminal.



.. toctree::
	   :maxdepth: 3

	   emsxFeatures
	   testEnv
	   stateDiagram
	   session
	   description
	   requestResponse
	   requestResponseSs
	   emsxSubscription
	   emsxHistory




