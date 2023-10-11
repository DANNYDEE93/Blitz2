### <ins>**OBSERVABILITY OF URL SHORTENER FUNCTIONALITY**</ins>
#### **Danielle Davis**
#### **September 30, 2023**




##### <ins>**PURPOSE**</ins>
_________________________

&emsp;&emsp;&emsp;&emsp;    "The Story: The URL Shortener has become very popular recently. The URL Shortener is growing in demand and you must ensure at least 14,000 users can access the application at any moment.   
You are in charge of deploying the new version of the application. The QA engineer will send 14,000 requests to the server. After the QA engineer tests your application, they will update you about the results.

purpose of the scenario was to max out cpu so nginx struggling to accept requests
if script was not on application consuming cpu, it would be able to accept 14000 requests



A summary of the QA's activities
worker processes = 8
2000
take comment out from multi

gzip on
make sure take the hashtag off to turn on gzip settings

- add ip address and port on file


* Added import logging to application.py file code
* log file name, logging format, and log level.
The filename 'app.log' means all logs will be written to this file.
The filemode 'w' means the file will be overwritten each time rather than appended to.

import logging
logging.basicConfig(filename='app.log', filemode='w', format='%(name)s - %(levelname)s - %(message)s')

________________________________

download stress ng 

run command: sudo nice -n -20 stress-ng --cpu 2 &

wasnt able to reach 3 users 


What steps were taken to remediation the issue (if there were any) 

you need to create a new instance with greater capacity!!:
		we need to automatically configuring our 	instances:: need script with all these installations 

  
What was the value of the test results before and after


/var/log/nginx/access.log
/var/log/nginx/error.log

Include screenshots of any monitoring or log information that helped you discover the issue (if there were any)

Nike emailed you about the URL Shortener. In Nike's email, Nike mentioned customers are complaining about the amount of time it takes to load their webpages. Nike will like us to reduce the amount of latency customers are experiencing." As an SRE engineer in this scenario, I need to test the functionality of the URL shortener associated with the web application. For Nike's standards and business needs, it isn't enough for the web application to just function. The application needs to be continuously optimized and tested to increase functionality as user traffic increases to the web page server. To truly measure the speed at which users can access the web application, I need to use a tool to measure the latency before and after our chosen solution with multiple tests to ensure latency is reduced. Overall, I need to improve the functionality of the services that the URL shortener for the Nike web application provides for Nike and its users. 




##### <ins>**LATENCY MEASUREMENT TESTING WITH JMETER**</ins>
________________

**Fill out Blitz Creation Form:**

- Test Plan Section: Choose 'basic traffic increase' under **Blitz package**
- Thread group section: Select **1000** for **number of threads (users)** (*simulated user traffic based on if users tried to access the URL shortener at the same time to see how the web  application can handle it*)
- Ramp-up period(seconds): 2
- Loop Count: 1
- Request Configuration Section: Choose *HTTP* protocol
- Server name or IP address: <URL shortener>
- Port number: 80 to make sure that you access the correct web application 
- HTTP request: GET to test the retrieval of information from the site 


&emsp;&emsp;&emsp;&emsp;    Using the same region and availability zone as where the web application was deployed from, I ran the first Jmeter latency test in the same region and availability zone. It took the test server 40.879ms to access the web application. This amount of latency is too high and does not meet Nike's standards because it’s causing a negative user experience according to their customer feedback. We want something to capture the traffic, so I created a CDN(content delivery/distribution network) through AWS CloudFront to establish interconnected servers across availability zones to reduce the distance between users and the webpage server.




##### <ins>**CREATING A CDN IN FRONT OF THE URL SHORTENER**</ins>
______________________________________

1. Go to CloudFront on AWS

2. Create distribution

3. Open elastic beanstalk where app is deployed --> copy domain *remove 'backslash at the end (\)*

4. Leave at HTTP only 

5. Port 80 

<ins>Scroll down to Cache Key and origin requests:</ins>

6. Click **cache policy** --> select **cache optimized** *(to cache static content)*

<ins>Scroll down to web application firewall (WAF):</ins>

7. Do not enable security protections *(A web applicaton firewall was not necessary for this test. In the future, I would want to enable this service to  protect my applications from miscellaneous activity that may negatively affect the application's functionality)*

<ins>Use edge location under settings:</ins>

8. Use all edge locations *(suggested best choice for performance because this service comes with established edge locations of servers and multiple backup server locations. In case one data center location of servers goes down, there are other locations that can take the web traffic. These locations are also cached through regional edge caches to store the regional locations of data centers.)*

9. Select **Create distribution**

10. Go back to list of distributions --> copy distribution domain name --> paste URL in another browser.

*[The web application was served up by the server quickly, but this is not an accurate measurement for the latency. Hence, the benefits of Jmeter to measure the latency experienced through simulated web traffic when accessing the web application across the internet]*

11. Take **distribution domain name** to run another Jmeter test




![Before & After CDN](https://github.com/DANNYDEE93/Blitz1/blob/main/Blitz1Diagram.jpg)




&emsp;&emsp;&emsp;&emsp;     After establishing a CDN to store the images and videos on the webpage of the application, I ran another Jmeter test that showed the amount of latency for the test server to reach the web application reduced to 9.652ms. The CDN helps further reduce latency by caching or temporarily storing the static content of the application which includes the web page so that users can access the content over the internet quicker. I reduced the communication across the internet for the users to reach the web page because the servers have less content to deliver with the CDN getting accessed before it, which ultimately decreases the web traffic to the server. Knowing where users are located helps SRE engineers know how to measure the amount of latency to optimize user experience through testing and updating application configurations.




##### <ins>**ALTERNATIVE WAYS TO CONTROL WEB TRAFFIC**</ins> 
________________________________________
-Create a new EC2 instance and load balancers to split up the traffic amongst multiple servers.

-Ping the application with multiple get requests to test the durability of the server to handle increased web traffic. This can check how long it takes for packets to be retrieved to measure latency.

-Increase availability zones to balance out the traffic if overwhelmed with requests.
