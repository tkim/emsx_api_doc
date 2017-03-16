############################
How to install serverapi.exe
############################


Please follow the following steps to install and register the installer with Bloomberg Enterprise Solutions with the assistance from EMSX Implementation team.

The following example is based on the windows environment and thus there will be slight variations when installing from linux or solaris operating system.


* Run serverapi.exe


.. code-block:: none
	
	C:\temp>serverapiinstaller.exe


* You will see the following message


.. code-block:: none

	C:\temp>serverapiinstaller.exe
	logging to C:\temp\install.2016102610.152444.log

	Bloomberg ECD Installer for Windows (32-bit)
	Version 3.2.2.0


	Warning: This program is protected by copyright law and international treaties.

	Unauthorized reproduction or distribution of this program, or any portion of
	it, may result in severe civil and criminal penalties, and will be prosecuted
	to the maximum extent possible under law.


	logging to C:\temp\install.2016102610.152444.log

	Bloomberg ECD Installer for Windows (32-bit)
	Version 3.2.2.0


	Warning: This program is protected by copyright law and international treaties.

	Unauthorized reproduction or distribution of this program, or any portion of
	it, may result in severe civil and criminal penalties, and will be prosecuted
	to the maximum extent possible under law.


	Would you like to continue? (Y/N): 


* Type:- Y


.. code-block:: none
	
	Would you like to continue? (Y/N): y

	Checking connectivity to Bloomberg ...


	a) via Bloomberg Network to [ Hostname = 208.134.161.62 Port = 8194 ] ...
	Succeeded.

	                            [ Hostname = 208.134.161.158 Port = 8194 ] ...
	Succeeded.

	                            [ Hostname = 208.134.161.18 Port = 8194 ] ...
	Succeeded.

	                            [ Hostname = 208.134.161.179 Port = 8194 ] ...
	Succeeded.

	b) via the Internet to [ Hostname = api1.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api2.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api3.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api4.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api5.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api6.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api7.bloomberg.net Port = 8194 ] ...
	Succeeded.

	                       [ Hostname = api8.bloomberg.net Port = 8194 ] ...
	Succeeded.



	Which of the above routes will you use to connect to Bloomberg? (a/b):


* Select the appropriate network option (private vs. public/internet)


.. code-block:: none
	
	Which of the above routes will you use to connect to Bloomberg? (a/b): b

	Bloomberg Network connectivity unavailable. Connecting via the Internet.

	Select Product Class
	1) blpddm    Software that provides development access to distribute data locally or contribute data to Bloomberg.
	2) ServerApi Provides access to Bloomberg real-time streaming and static data
	0) Quit

	Please enter selection:


* Select:- 2 for Server API


.. code-block:: none

	Please enter selection: 2

	Installation path:
	        'C:\'

	Use this path? (Y/N/Q): 


* Select:- Y


.. code-block:: none

	Use this path? (Y/N/Q): y

	Downloading latest installer ...
	done.

	logging to C:\temp\install.2016102610.152444.log

	Beginning new install ...


.. note::
	
	If the default port is already being used by a different service it may show the following message:


.. code-block:: none

	*** WARNING: Port conflict detected with other service.
	The port of the Desktop will conflict with the ServerApi should installation proceed. If you still want to install
	ServerApi, you will need to specify a different port number.

	 Do you want to continue with the installation? (Y/N) [N]:


* Select:- Y and enter the port


.. code-block:: none
	
	Do you want to continue with the installation? (Y/N) [N]:y
	Please enter ServerApi listen port: [8294]:8294


* Select the version:-

.. code-block:: none

	Versions available for ServerApi
	1) 3.46.6.0     Windows     ServerAPI 2014-07

	2) 3.48.8.1     Windows     ServerAPI 2014-09

	3) 3.48.9.1     Windows     ServerAPI 2014-11

	4) 3.50.7.1     Windows     ServerAPI 2015-01

	5) 3.56.4.1     Windows     ServerAPI 2015-04

	6) 3.60.0.1     Windows     ServerAPI 2015-07

	7) 3.64.5.1     Windows     ServerAPI 2015-10

	8) 3.70.0.1     Windows     ServerAPI 2016-01

	9) 3.72.2.1     Windows     ServerAPI 2016-04

	10) 3.82.3.1     Windows     ServerAPI 2016-10

	11) 3.46.6.0     Windows64   ServerAPI 2014-07

	12) 3.48.8.1     Windows64   ServerAPI 2014-09

	13) 3.48.9.1     Windows64   ServerAPI 2014-11

	14) 3.50.7.1     Windows64   ServerAPI 2015-01

	15) 3.56.4.1     Windows64   ServerAPI 2015-04

	16) 3.60.0.1     Windows64   ServerAPI 2015-07

	17) 3.64.5.1     Windows64   ServerAPI 2015-10

	18) 3.70.0.1     Windows64   ServerAPI 2016-01

	19) 3.72.2.1     Windows64   ServerAPI 2016-04

	20) 3.82.3.1     Windows64   ServerAPI 2016-10

	0) Quit
	Please enter version of ServerApi that you want to install:


* Select the latest:-


.. code-block:: none

	Please enter version of ServerApi that you want to install: 20
	Downloading ServerApi components ...


* Enter other information:-

		Enter the following information:

	        Country (e.g., USA): 
	        State (e.g., NY): 
	        City or Town (e.g., New York): 
	        Company Name (e.g., Bloomberg L.P.): 
	        Department Name (e.g., Equity Trading): 


* Finished:-

.. code-block:: none

		Enter the following information:

	        Country (e.g., USA): USA
	        State (e.g., NY): NY
	        City or Town (e.g., New York): New York
	        Company Name (e.g., Bloomberg L.P.): Bloomberg LP
	        Department Name (e.g., Equity Trading): EMSX

		Creating certificate ...
		done.

		Registering server ...
		done.


		Do you want to install ServerApi as a Windows Service? (Y/N): y

		Installing ServerApi as a windows Service...
		service ServerApi configured for restart on first error
		 done

		done.


		*** Please reboot your computer for changes to take effect ***


		Call Bloomberg's Global Customer Support at +1 (212) 318-2000 and ask for the
		Global Installs desk. The Bloomberg representative will ask you to read your
		registration number over the phone four characters at a time.


		Your registration key is:
		        321c-5ad5-7fa8-2954-1930-abb0-b64c-ecaf-1505-64d4


.. note::

	Once the registration process is completed. EMSX Implementation team globally will assist with configuring the Server Side EMSX API with various execution destinations per client request.

