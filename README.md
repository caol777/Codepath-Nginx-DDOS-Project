# Codepath-Nginx-DDOS-Project

In this project we ran a NGINX webserver on port 80 and used their amplify site to monitor the web traffic. This led us to rocognize signs of ddos attacks and what we could do with nginx to mitigate the effects of a ddos attack.

In this project we are using the tool slowloris and running it on localhost ( where our webserver is located) and we will be looking at the effects of it on our amplify graphs.

At this moment this is how our nginx/conf.d/default.conf file looks.

![Screenshot from 2024-10-18 11-18-27](https://github.com/user-attachments/assets/f4706313-623d-42d5-90a1-58d5f254e54f)

This is the responsible file that we will be using to impliment our DDOS mitigation.

Now that we showed our default config file its time to start our slowloris attack

![Screenshot from 2024-10-18 11-25-08](https://github.com/user-attachments/assets/25c66b95-a4ec-42f8-948c-c8fadbf8dee0)

After about ten minutes we should be able to see a full display on how the ddos affected the site.

For the next 3 screenshots the data we will be focusing on will start at 11:40 on the timeline.

![Screenshot from 2024-10-18 12-02-00](https://github.com/user-attachments/assets/b447ed81-d716-4337-a55f-7bed6d51e87a)

As you can see we are getting a constant flip of either 2.50 requests/connections or zero. This is because the connections go from request to current connections and stay there as is a slow attack.

![Screenshot from 2024-10-18 12-02-04](https://github.com/user-attachments/assets/17a0321b-232a-4e84-92da-1cd9bb221b27)

In this screenshot we see that the number of HTTP errors are low which is a bad thing because this means that these malicious connections are going through freely. 
There is also the File Descriptors which are abnormally high due to the attack.

![Screenshot from 2024-10-18 12-02-23](https://github.com/user-attachments/assets/b82729f0-f8b2-4ff6-ae5a-de9060bed780)

In this screenshot we see as our attack started the network traffic had a spike and the disk I/O speeds would have dips and im assuming thats because of the constant load the server is handling.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## The Revised config file.

This is the revised config file


![Screenshot from 2024-10-18 14-34-34](https://github.com/user-attachments/assets/ec9e08e7-6e58-489f-8eb6-1a773d78c123)


1. Connection Limiting: 
The limit_conn_zone
directive limits the number of simultaneous connections from a single IP address to 10. This can help prevent a single IP from overwhelming your server with too many connections.

2. Request Rate Limiting:
Thelimit_req_zone
directive sets a rate limit of 5 requests per second per IP address, with a burst capacity of 10 requests. This can help control the rate of incoming requests and mitigate certain types of DDoS attacks, particularly those that involve high request rates.

4. Status Monitoring: The /nginx_status
location allows you to monitor the server's status, which can be useful for identifying unusual traffic patterns or potential attacks. However, it is restricted to localhost, which is a good security practice.

5. Timeouts: The client_body_timeout and client_header_timeout
  These two commands aid with closing slow connections based off of the amount of times a connection is trying to write data. Since our attack is using slowloris the connections are slow and write infrequently and these timeouts will put a stop on those connections.

6. Keepalive Timeout: The keepalive_timeout is set to 5 seconds, which is reasonable for maintaining persistent connections lowering this keep alive can help lower our current requests/connections.

## The Results (14:43 start)

![Screenshot from 2024-10-18 15-27-04](https://github.com/user-attachments/assets/1c6a7f89-c669-48d5-8007-a42ea5fe3cc8)


![Screenshot from 2024-10-18 15-27-16](https://github.com/user-attachments/assets/bdb0ef3f-bf2a-4ce8-9b1e-f1242d682aa2)


![Screenshot from 2024-10-18 15-27-24](https://github.com/user-attachments/assets/f6571eba-33d9-4fc8-a2c1-182e3f0d9338)


As you can see the current connections went down drastically and things like our file descriptors stayed at a constant 273 thru out the attack period.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now onto the PCAP files.


The question for these files was which file has DDos mitigation setup on their network based off of the pcap files. 

This is pcap A

![image](https://github.com/user-attachments/assets/538649a0-edb6-4ffc-aa62-e75a20143056)

And this is pcap B
![image](https://github.com/user-attachments/assets/c6d6d55e-4455-4fbf-85b6-e461305e125d)



I would say that the A.pcap file represents the server with DDoS mitigation set up. While it contains more packets than B.pcap, it also includes packets that are not present in B.pcap. Notably, the TCP RST packets are found in B.pcap. At first glance, this might appear to be an error; however, upon closer inspection, it becomes clear that these packets are dropping connections from HTTP GET requests. This indicates that A.pcap has mechanisms in place to drop connections for certain parts of the web server. Additionally, there was a difference in the speed at which the packets were logged. On average, A.pcap recorded packets more quickly, while B.pcap lagged behind. B.pcap seemed too tame, and given the context of our project, it is evident that it was not as secure as A.pcap
