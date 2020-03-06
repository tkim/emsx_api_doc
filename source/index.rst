.. emsx_api_doc documentation master file, created by  Terrence C. Kim :caption: Contents:
   
##########################
EMSX API Programmers Guide
##########################

This document is for developers who will use the Bloomberg EMSX API to develop custom applications. 

The Bloomberg EMSX API is available as desktop API and server-side API. The desktop API requires full Bloomberg terminal to use.

The Bloomberg API uses an event-driven model. The EMSX API is an extension of Bloomberg API 3.0 and it lets users integrate streaming real-time and static data into their own custom applications. The user can choose the data they require down to the level of individual fields. The Bloomberg API 3.0 programming interface implementations are extremely lightweight. For details to the Desktop API, please refer to the Desktop API Programmers Guide from ``WAPI<GO>``.

The Bloomberg API interface is thread-safe and thread-aware, giving applications the ability to utilize multiple processors efficiently. The Bloomberg API supports run-time downloadable schema for the service it provides, and it provides methods to query these schemas at runtime. This means additional service in Bloomberg API is supported without addition to the interface.

The object model for Java, .NET and C++ are identical. The C interface provides a C-style version of the object model.


.. important::
   
  The Bloomberg EMSX API requires the full understanding of how Bloomberg ``EMSX<GO>`` function works within the Bloomberg terminal. Before 
  starting on any EMSX API, please have your local EMSX representative provide a full training of ``EMSX<GO>`` function. This documentation 
  does not include any details on how ``EMSX<GO>`` works.



.. warning::
  
  Please note that performance/load test should never be performed on any of the API environment as this is a shared environment and we monitor and increase capacity as needed.



.. toctree::
   :maxdepth: 3
   :caption: Table of Contents

   introduction
   server/index
   programmable/index
   mifid2
   other
   faq
   glossary


.. :ref:`genindex`
.. :ref:`modindex`
.. :ref:`search`


