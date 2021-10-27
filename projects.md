
## Current Project
- I am currently working with Citigroup, under treasury and trade solutions group.
- Suite of application called Citiconnect.
- Which is a single channel solution for transmission and reciept of files for Citi's clients.
- This application allows clients to send payment files from their ERP system directly to citi.
- Citconnect supports all major international standard formats such as ISO20022 XML, EDIFACT, SAP IDOC.
- Citiconnect is a global product which is available in more than 96 countries.
- The average volume is 10 million transactions per day worth more than a billion dollars per day.

- Techonology wise, the application has a java front end application, the field mapping and validations are done in abinitio which is an etl tool. and we use ibm computegrid for batch processing.

## Why are you looking for a new job?
I have been working for the same company for around 7 years now. I want to avoid my job skills becoming stagnant in my current assignment, I have decided to seek out a new career that will allow my professional interests to grow.


## ESession  (Performance Issue)
- ESession is a legacy framework level class.
- ESession is the entry point class in the server side which maintains user’s session information which is created during user logins and will be used for processing of requests on a particular application instance. 
- All the request from client to server will land in this class and the model/business classes with respective to the request will be invoked to process request. 
- ESession processes all kinds of requests like Lookups and Entitlement Validation.
- ESession is not thread safe.
- Only one instance of it can be used for each user session. 
- Hence current use of this class involves costly synchronization of requests which effectively queues all requests to be processed one after another.
- This limitation also forces the web applications to use sticky sessions.  

- A Gemfire cache will be used to avoid expensive DB calls at the initialization of the ESession object and processing of request with the initialized object.

ESession Stickiness removal to support the increasing volumes.



   
