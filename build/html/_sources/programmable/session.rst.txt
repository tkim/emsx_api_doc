##############
Session Object
##############

Connecting and creating a session object for EMSX API uses BBCOMM for desktop and AuthID or EMRSID + IP Address for server side EMSX API access.

BBCOMM is the service that runs on an EMSX user’s computer and conducts all communication to and from Bloomberg. The application connects to the local BBCOMM and the most common configuration is “localhost” for hostname and “8194” for port number. 

If the application is not able to establish a connection to the local BBCOMM the call to ``session.start()`` will fail and return false. If the connection to the emapisvc service fails, OpenService call will return false.


EMSX API & Correlation ID
=========================

The ``CorrelationID`` ties the subscriptions and request response messages. The user would have to inspect the result to identify the source of the data and handle the message or the errors. Using the CorrelationID the user can immediately tell if it is emapisvc or mktdata. The ``CorrelationID`` is unique to the subscription only and not to the orders and routes. 

The ``CorrelationID`` s are set when you send the request or submit the subscription. The ``CorrelationID`` s belong to the message.  When an event fires, which is passed to the handler, this opens up the event and iterate through the message(s). There can be more than one message per event. Each message (MessageDataType) has a ``.correlationID`` property.  The ``CorrelationID`` (CorrelationID datatype) is specified to a value and once the user submit it with the request in ``sendRequest`` call or when the user adds it to the individual subscription in the subscriptions list prior to the ``session.subscribe`` call. 

