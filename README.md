# Codepath-Nginx-DDOS-Project

In this project we ran a NGINX webserver on port 80 and used their amplify site to monitor the web traffic. This led us to rocognize signs of ddos attacks and what we could do with nginx to mitigate the effects of a ddos attack.

In this project we are using the tool slowloris and running it on localhost ( where our webserver is located) and we will be looking at the effects of it on our amplify graphs.

At this moment this is how our nginx/conf.d/default.conf file looks.

![Screenshot from 2024-10-18 11-18-27](https://github.com/user-attachments/assets/f4706313-623d-42d5-90a1-58d5f254e54f)

This is the responsible file that we will be using to impliment our DDOS mitigation.

Now that we showed our default config file its time to start our slowloris attack

![Screenshot from 2024-10-18 11-25-08](https://github.com/user-attachments/assets/25c66b95-a4ec-42f8-948c-c8fadbf8dee0)

After about ten minutes we should be able to see a full display on how the ddos affected the site.
