# squid-proxy-install
Squid is a Linux-based proxy application. The Squid proxy server is used for filtering traffic, security, and DNS lookups.

Also, Squid can speed up a web server by caching resources. The Squid Proxy allows a server to cache frequently visited web pages. When the user requests a web page or file, the request goes directly to the proxy server — an intermediary device between the user’s device and the internet. The proxy server pulls up the resources and relays them to the user.

This guide will walk you through how to set up and install Squid Proxy server on Ubuntu.

Installing Squid Proxy on Ubuntu
Step 1: Refresh the Software Repositories

Ensure you’re working with the latest software version available.

Launch a terminal window, and enter the following:

Code
sudo apt-get update

Step 2: Install Squid Package on Ubuntu

To install Squid, run the command:

Code
sudo apt-get install squid

The system should prompt for confirmation – enter Y and allow the process to complete itself.
Configuring Squid Proxy Server

The Squid configuration file is found at /etc/squid/squid.conf.

1. Open this file in your text editor with the command:

Code
sudo nano /etc/squid/squid.conf

2. Navigate to find the http_port option. Typically, this is set to listen on Port 3218. This port usually carries TCP traffic. If your system is configured for traffic on another port, change it here.

You may also set the proxy mode to transparent if you’d like to prevent Squid from modifying your requests and responses.

Change it as follows:

Code
http_port 1234 transparent

3. Navigate to the http_access deny all option. This is currently configured to block all HTTP traffic. This means no web traffic is allowed.

Change this to the following:

Code
http_access allow all

4. Navigate to the visible_hostname option. Add any name you’d like to this entry. This is how the server will appear to anyone trying to connect. Save the changes and exit.

5. Restart the Squid service by entering:

Code
sudo systemctl restart squid

Configure Squid Client

All this configuration has been done to set up your Squid proxy server. Now, switch to your client machine and open your web browser.

If you’re using Firefox, you can find the proxy settings under:

Menu > Options > Network Settings > Settings

Tick the radio button for Manual proxy configuration.

If you’ve entered a hostname in Step 4, you should be able to enter that name plus the port you have designated. Otherwise, use the IP address for the system hosting your Squid proxy.

To test it, you can visit https://whatismyipad...s.com/ip-lookup – if your proxy is working, your IP address should display as the proxy server’s IP address.
Add Squid ACL

Note: After each of these steps, you should save and exit, then restart the Squid service to apply the new configuration.

Create an access control list by editing the squid.conf file again, as in Step 4.

Add a new line as follows:

Code
acl localnet src 192.168.0.15

This will create a rule that only allows the system at this IP address to connect. It is recommended that you comment the line to identify the rule:

Code
acl localnet src 192.168.0.15 # test computer

Anything after the # sign is ignored by Squid.

You can specify a range of IP addresses as follows:

Code
acl localnet src 192.168.0.15/30

Open Ports

To open a specific port, add the following:

Code
acl Safe_ports port 123 # Custom port

Configure Proxy Authentication

This forces users to authenticate to use the proxy.

Start by installing apache2-utils:

Code
sudo apt-get install apache2-utils

Create a passwd file, and change the ownership to the Squid user proxy:

Code
sudo touch /etc/squid/passwd

sudo chown proxy: etc/squid/passwd

Add a new user and password

1. To add a new user to Squid, use the command:

sudo htpasswd /etc/squid/passwd newuser

The system will prompt you to enter and confirm a password for newuser.

2. Edit the /etc/squid/squid.conf file, and add the following command lines:

Code
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd

auth_param basic children 5

auth_param basic realm Squid Basic Authentication

auth_param basic credentialsttl 2 hours

acl auth_users proxy_auth REQUIRED

http_access allow auth_users

Block Websites on Squid Proxy

1. Create and edit a new text file /etc/squid/blocked.acl by entering:

Code
sudo nano /etc/squid/blocked.acl

2. In this file, add the websites to be blocked, starting with a dot:

.facebook.com

.twitter.com

Note: The dot specifies to block all subsites of the main site.

3. Open the /etc/squid/squid.conf file again:

Code
sudo nano /etc/squid/squid.conf

4. Add the following lines just above your ACL list:

Code
acl blocked_websites dstdomain “/etc/squid/blocked.acl”

http_access deny blocked_websites

Commands When Working with the Squid Service

To check the status of your Squid software, enter:

Code
sudo systemctl status squid

This will tell you whether the service is running or not.

To start the service enter:

Code
sudo systemctl start squid

Then set the Squid service to launch when the system starts by entering:

Code
sudo systemctl enable squid

You can re-run the status command now to verify the service is up and running.

To stop the service, use the command:

Code
sudo systemctl stop squid

To prevent Squid from launching at startup, enter:

Code
sudo systemctl disable squid

Conclusion

If you’ve followed along closely, you should now have a basic understanding of how Squid works, and how to install and configure Squid Proxy on Ubuntu.

Proxy servers are a valuable tool for securing network traffic, preventing attacks and restricting access.
