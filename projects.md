
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


### Sticky Sessions
- When your website is served by only 1 web server, for each pair, a session object is created and remains in the memory of the web server.
- All the requests from the client go to this web server and update this session object.
- If some data needs to be stored in the session object over the period of interaction, it is stored in this session object and stays there as long as the session exists.

- However, if your website is served by multiple web servers which sit behind a load balancer, the load balancer decides which actual (physical) web-server should each request go to.
- For example, if there are 3 webservers A, B and C behind the load balancer, it is possible that www.mywebsite.com/index.jsp is served from server A,www.mywebsite.com/login.jsp is served from server B and www.mywebsite.com/accoutdetails.php are served from server C.

- Now, if the requests are being served from (physically) 3 different servers, each server has created a session object for you and because these session objects sit on 3 independent boxes, there's no direct way of one knowing what is there in the session object of the other.
- In order to synchronize between these server sessions, you may have to write/read the session data into a layer which is common to all - like a DB. Now writing and reading data to/from a db for this use-case may not be a good idea.
- Now, here comes the role of sticky-session. If the load balancer is instructed to use sticky sessions, all of your interactions will happen with the same physical server, even though other servers are present.
- Thus, your session object will be the same throughout your entire interaction with this website.
- To summarize, In case of Sticky Sessions, all your requests will be directed to the same physical web server while in case of a non-sticky loadbalancer may choose any webserver to serve your requests.

- Persistence is the process of ensuring that a user is connected to the same server every time they make a request within the boundaries of a single session.
- Even though users could be persisted based on their IP address, this is rarely done due to the sharing of IP addresses. 
- Persistence is most often implemented using a cookie containing the server session id because it is the most accurate method of determining where a user's session is currently stored.

- If you're a web developer or administrator, you might think "that sounds a lot like server affinity".You'd be right, of course, server affinity and persistence are two different terms that mean the same thing.
- Sessions are stored on the server, and are not reliant on cookies being enable in the client's browser. Sessions are where web developers store bits of application relevant data that they may wish to use across requests. Shopping carts are the most ubiquitous example of session data, but there are other uses for it, especially in complex web applications like CRM (customer relationship management) or SFA (sales force automation) applications.
- Cookies store bits of data on the client (the browser) and are passed to the server via the HTTP header Cookie.

- The most common data used to persist connections is SSL session id. SSL connections without persistence is like crust without the bread. Yeah, it's that bad. Basically, load balancing SSL without persistence doesn't work.
- The second most common data used to persist connections is application or server session id, like **JSESSIONID** or PHPSESSIONID. These IDs are automatically generated by applications and web servers, and are generally passed to the client as a cookie on the first response, and then used by the load balancer to determine to which server it should direct subsequent requests.
- An example of HTTP headers storing a JSESSIONID in a cookie:

   `Cookie: JSESSIONID=9597856473431   Cache-Control: no-cache   Host: 127.0.0.2:8080   Connection: Keep-Alive`
   
