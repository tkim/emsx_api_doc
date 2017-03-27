####################
Server Side EMSX API
####################

EMSX API is available for use via both the desktop (Desktop API, or DAPI) and via a server-side endpoint known as EMSX API Server. The first relies on a logged in Bloomberg terminal for it's connection, whereas the server does not. This makes DAPI unsuitable for mission critical applications.

However, the service schema is the same across the two platforms.  This means that the code base for an application which was developed on the desktop API is capable of working on the server-side solution without changes to the underlying business logic.

All that is required to move desktop EMSX API applications to the server is the addition of code needed to perform user authentication.


.. toctree::
	:maxdepth: 3

	migration
   	serverApiInstallation


