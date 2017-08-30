EMSX Features
=============

The EMSX API supports 99% of the features supported in EMSX<GO> function. 

One of the few exceptions to this is the EMSX Pairs Ticket. Currently, the EMSX API does not support the EMSX Pairs Ticket features. 
However, most EMSX API users are still able to trade pairs using the old EMSX ticket logic or by incorporating their own pairs logics on top of the EMSX API.


EMSX Teams
==========

The EMSX API allows the same action on ``TEAMVIEW`` as you would have permission on EMSX<GO> function.

The ``TEAMVIEW`` feature in EMSX<GO> allows a team member to view or take action on behalf of the team members based on the team setting within EMSX<GO>.

For EMSX API, This offers flexibilities within the application design. For example, a single subscription with the team name can capture all the events for the team members. The topic string for using team remains the same as non-team with the exception of adding team name on the topic string as illustrated below.

.. important::

	//blp/emaspivc/order; **team=my_team_name** ?fields=EMSX_ASSIGNED_TRADER, EMSX_BASKET_NAME, EMSX_CFD_FLAG, EMSX_AMOUNT


Trading on behalf of team members from ``TEAMVIEW`` requires creating a route on behalf of the team member. The service object of type ``RouteEx`` and fill in the required fields before submitting the request.

Within ``RouteEx``, there is an element ``EMSX_TRADER_UUID`` where the user can enter the order owner's Bloomberg UUID. Bloomberg will do the validation against the user privilege setup via 
EMT<GO> and EMBR<GO>.

A user can be part of more than one team on the backend. When the user creates the topic string and does not belong to a team or specify a wrong team,  the user will receive an error.

In cases where a user is defined as a member of multiple teams, then the user will need to supply multiple subscriptions. (One for each team). These subscriptions should be monitored separately since the user will receive two notifications. 


EMSX Element Definitions
========================

For information on accessing field meta data, this is currently only supported within Bloomberg terminal.

The user will need to access FLDS<GO> function within the Bloomberg terminal. Once in FLDS<GO>, type EMSX underneath the security section and choose EMSX under the filter. The source is Calcrt and should select All for Field Type.


