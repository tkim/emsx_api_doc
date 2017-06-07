Accessing the Test Environment
==============================

Bloomberg provides a test environment for clients to build and test their strategies using the EMSX API.

This is accomplished by referencing //blp/emapisvc_beta as the service name in your program. This command will allow your service  to 
redirect all EMSX API requests and subscriptions to the test environment.

Once the client has thoroughly tested the custom-built strategies, they can access the production environment by changing the service 
name from //blp/emapisvc_beta to //blp/emapisvc.

Inside the Bloomberg Terminal type DGRT Y087<GO> {Y (number zero) 87} This command allows the particular terminal window to log into the 
beta environment. Please note, when a user is remote into the beta environment it only affects that particular terminal window and the 
other screens will not be affected by the DGRT command.

To check which environment your current view is in, type VSAT <GO> inside the Bloomberg terminal.

To get back to production type DGRT OFF <GO>. Please note that the testing environment in Beta will not operate in the exact same way as 
the production environment. Also, please note that the beta environment is a lot slower than the production environment.

To launch the ticket using limited functional terminal will require different command once inside the Y087 environment. EMSX /NOLP <GO> - 
This command disables Launchpad ticket from EMSX.