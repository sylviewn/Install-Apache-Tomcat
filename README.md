# Install-Apache-Tomcat
Steps to install Apache Tomcat on Ubuntu 22.04 Linux


# Install-Apache-Tomcat
Steps to install Apache Tomcat on Ubuntu 22.04 Linux


1. Install OpenJDK- Java
First  set up an open-source Java Development kit on Ubuntu 22.04 LTS using the terminal.

Before moving further, run the system update command once.

sudo apt update
The java version supported is 8 or above. Here we are getting Java 11.

sudo apt install default-jdk
To check and confirm, that Java has been installed successfully, see its version…

java -version
 

2. Download Apache Tomcat 
    You can get the latest version directly from the official webpage of Tomcat. 
    wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.8/bin/apache-tomcat-10.1.8.tar.gz


3. Install Apache Tomcat on Ubuntu 22.04
Extract the downloaded file to /opt directory so that we won’t delete it accidentally.

1. Create a directory under /opt to extract files.

sudo mkdir -p /opt/tomcat
2. Now, extract the downloaded Tomcat Tar file into the created directory.

sudo tar xzvf apache-tomcat-*tar.gz -C /opt/tomcat --strip-components=1
Create a dedicated user

To ensure the security of the system while testing various web applications, let’s create a non-root user that has only access to the created /opt/tomcat folder.

sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat


4. Assigning Tomcat user permissions
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh'


5. Create a Systemd service file
By default, we won’t have a Systemd unit file for Tomcat to run it in the background and to easily stop, start and enable its services. Thus, we create one, so that we could manage it without any issues.

Create Systemd unit file

sudo nano /etc/systemd/system/tomcat.service

Paste the following block of code in it:
[Unit]
Description=Tomcat webs servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat
RestartSec=10
Restart=always 
Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"

Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
[Install]
WantedBy=multi-user.target

To save the press Ctrl+X, type –Y, and hit the Enter Key.
Note: In the above-given code for creating a systemd file, we have to mention the path of Java. However, the given one in the above code is the default path, still, to confirm the same you can run the below command:

sudo update-java-alternatives -l



6. Enable and start Tomcat service on Ubuntu 22.04
Finally, we have plugged in all the necessary things to start the Tomcat service in the background on Ubuntu 22.04 LTS Jammy. Let’s enable and run the same.

sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
To confirm everything is working normally, check the status of service:

systemctl status tomcat --no-pager -l



7. Add Roles and Admin username and password
This step is important, without performing it we will get an error: “403 Access Denied on Tomcat 10/9/8 error” as we click on “Server Status“, “Manager App” and “Host Manager” links on the Apache Tomcat Web interface.

Edit user configuration file:
sudo nano /opt/tomcat/conf/tomcat-users.xml


(At the end just before </tomcat-users> tag copy and paste the following lines.
Note– Change the username and password values with whatever you want to set for your Tomcat.)

<role rolename="admin"/>
<role rolename="admin-gui"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>
<user username="h2s" password="pwd" roles="admin,admin-gui,manager,manager-gui"/>

Save the file and exit- Ctrl+X, type- Y, and hit the Enter key.


8. Enable Tomcat and Host Manager Remote access
By default, you won’t be able to access your installed Tomcat Manager sections (web interface) outside the local system. For that, we have to enable it by editing individually the context.xml file available for Tomcat Manager and Host Manager.

For Tomcat Manager’s remote access:

Edit the Context file  

sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
In the file, scroll and go to the end and comment out the following block of text-

<Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
Just add <!-- at the beginning and --> in the end, after that, this will look something like this-

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Save the file and exit- Ctrl+X, type- Y, and hit the Enter key.




8. Enable Tomcat and Host Manager Remote access
By default, you won’t be able to access your installed Tomcat Manager sections (web interface) outside the local system. For that, we have to enable it by editing individually the context.xml file available for Tomcat Manager and Host Manager.

For Tomcat Manager’s remote access:

Edit the Context file  

sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
In the file, scroll and go to the end and comment out the following block of text-

<Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
Just add <!-- at the beginning and --> in the end, after that, this will look something like this-

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Save the file and exit- Ctrl+X, type- Y, and hit the Enter key.


 
9. Open port 8080 in Ubuntu 22.04 Firewall
The service to access the web interface via browser is available on port 8080 and to access the same remotely using any other system, we have to allow its outgoing connection in the firewall.

sudo ufw allow 8080
 
 
 
 
 10. Access the Tomcat Web interface
Open any browser on the local or remote system and point it to the IP address or domain of the server where you have installed the Apache Tomcat.

For example:

http://server-ip-addres:8080
or 
http://youdomain.com:8080


