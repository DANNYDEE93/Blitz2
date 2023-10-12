### <ins>**TESTING UX RELIABILITY **</ins>
#### **Danielle Davis**
#### **October 9, 2023**




##### <ins>**PURPOSE**</ins>
_________________________

&emsp;&emsp;&emsp;&emsp;   	Scenairo: The URL Shortener has become very popular recently. The URL Shortener is growing in demand and you must ensure at least 14,000 users can access the application at any moment. I am in charge of deploying the new version of the application. After the QA engineer sendt 14,000 requests to the server to test my application, he updated me of the results and retested my server after ensuring 14,000 requests are able to reach my instance. The main purpose of the scenario was to max out CPU usage to make my nginx server, delivered on port 5000, struggle to accept requests. This is important to test for performance of my server to accept a high amount of requests at a time.



### Steps Taken: 
___________________________

Before the QA's test, I had to change the nginx config file to allow greater requests to my server, add import logging into my configuration file, and download stress ng to allow my ip address to be accessed by the QA:

![nginx.conf](https://github.com/DANNYDEE93/Blitz2/blob/main/nginfile.png)

* worker processes = 8
* worker connections = 2000
* allow multi accept for requests
* gzip setting on [level 6 requires a greater amount of CPU processing power which is why a greater instance type is necessary to ensure my server can handle 14000 requests]

![gzipsettings](https://github.com/DANNYDEE93/Blitz2/blob/main/nginxfile1.png)

* Added import logging to application.py file code
* the configuration below allows my server to create a log report along with a file name, logging format, and log level.
[all logs will be written to a app.log file]
[filemode = 'w' will overwrite each time a new log is recorded rather than appending to it]
[simple log level]
* import logging
logging.basicConfig(filename='app.log', filemode='w', format='%(name)s - %(levelname)s - %(message)s')

![logging](https://github.com/DANNYDEE93/Blitz2/blob/main/importlogging.png)

* download stress ng before running script

![stressng](https://github.com/DANNYDEE93/Blitz2/blob/main/stresscommand.png)

* run command in separate blitz.sh file: sudo nice -n -20 stress-ng --cpu 2 & [this allows testing the performance of my server under load by simulating max CPU utilization. The "&" runs the command in the background so the terminal remains usable]
* gave my IP:address & port to QA

*  QA sent 14000 requests & 3 users were not able to reach my URL shortener.

### Resolving Server User Access 
______________________________________

All of the changes in this test created a greater you need for more CPU utilization]

*  Cd into **/var/log/nginx/error.log**:
2023/10/10 20:24:42 [error] 501#501: *1 connect() failed (111: Unknown error) while connecting to upstream, client: 167.94.145.53, server: _, request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8000/", host: "3.83.232.97:5000"

This error message shows that the the ip address that tried to access my server sent a GET request but it could not access the host ip address because of the upstream ip address on port 8000.

* Go to instance on AWS console --> **Actions** --> **Instance Settings** --> **Change instance type**
* Increased capacity of instance type to t3.xlarge to strengthen my server to handle the entire 14,000 GET requests sent to my web application
* 	Compared to the t2.medium instance type, it allows for greater CPU and memory to handle a greater workload, greater network bandwidth with 5 Gbps, and faster disk storage to allow the server to process faster responses at a greater capacity.
* 	Compared to a larger instance such as a m6g.medium which allows for 10 gbps of network bandwidth and greater processing, I may be in 	danger of wasting resources. It is important to know when to scale as needed.

![systemdiagram](https://github.com/DANNYDEE93/Blitz2/blob/main/blitz2.jpg)


After retesting my server on a t3.xlarge instance type,



