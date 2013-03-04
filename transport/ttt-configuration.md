Transport Testing Tool Configuration
====================================

This document describes how to get the Trandport Testing Tool (TTT) configured
on your own hardware or instance.

Last updated: February 22, 2013 by Alan Viars

Overview:
---------

You MUST use Ubuntu 12.04 LTS 64-bit Server Edition.  No other operating systems
are supported at this time. The high-level tasks are as follows:

1. Setup a new server with Tomcat7 and a public facing IP address.
2. Create a host name and configure DNS for your host.
3. Download and decompress the "tttdir" package.
4. Setup a Tomcat Connector for port 8443
5. Adjust the settings in the "tk_props.txt" file.
6. Download and Install the ttt war.
7. Create/Obtain certificates for your domain
8. Install certificatse in the TTT
9. Start the SMTP daemon.
10. Create the output folder for Direct validation messages.
11. Test the server.


1. Setup a new server with a public facing IP address:
------------------------------------------------------

Setup a new server with a public facing IP address.  We are using Ubuntu
12.04 64-bit Server Edition.  Specifically, we are using the AWS AMI:
ami-3d4ff254.

Open the following inbound ports on your firewall.

    TCP Port (Service)	Source
    22 (SSH)	        0.0.0.0/0	
    25 (SMTP)           0.0.0.0/0
    8080 (HTTP*)        0.0.0.0/0
    8443 (HTTPS*)       0.0.0.0/0


After the server is setup, log into the server and install Tomcat7. This will
also install OpenJDK. We assume the user you are going to use to login is
"ubuntu" and this user has sudo privileges. This is consistent with Canonical's
Ubuntu AWS EC2 instances. Throughout this document replace "your-domain.com"
with your actual domain name.

    ssh -i .ec2/ttt.pem ubuntu@ttt.your-domain.com
    sudo apt-get update
    sudo apt-get -y install tomcat7 tomcat7-admin nmap


2. Create a host name and configure DNS for your host:
------------------------------------------------------

Setup a static ip and a domain name. Create a static IP and associate  it with
a domain name. In this example, we will use the IP  "123.123.123.123" and the
hostnme "ttt.your-domain.com".  

Setup the A, CNAME as show below.
 
    Type:           Source:                       Destination:
    -----------     ----------------              ---------------
    A Record	    ttt.your-domain.com           123.123.123.123
    A Record	    mail.ttt.your-domain.com      123.123.123.123	
    MX Record       mail.ttt.your-domain.com      123.123.123.123
    MX Record       smtp.ttt.your-domain.com      123.123.123.123	


3. Download and Decompress the "tttdir" package.
------------------------------------------------

Note the following instructions may vary a bit if you are not using AWS EC2.

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.your-domain.com

Create the following directory and subdirectories from your home directory:

    wget http://sourceforge.net/projects/iheos/files/TransportTestingTool/version-160/tttdir-2013-02-15.tar.gz/download -O tttdir-2013-02-12.tar.gz
    tar zxvf tttdir-2013-02-15.tar.gz


4.  Setup a Tomcat Connector for port 8443
------------------------------------------

Open Tomcat 7's server.xml configuration file for editing.

    sudo nano /etc/tomcat7/server.xml
    
Locate the commented out section that looks like this:

    <!--
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               clientAuth="false" sslProtocol="TLS" />
    -->

Replace it with the following text:

    <Connector port="8443"
            protocol="HTTP/1.1"
            maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="/home/ubuntu/tttdir/external_cache/keystore" keystorePass="changeit"
           truststoreFile="/home/ubuntu/tttdir/external_cache/keystore" truststorePass="changeit"
           clientAuth="true" sslProtocol="TLS"
     />

Press Ctrl-x, and then "Y", to exit and sabve the changes.


Restart Tomcat7.
    
    sudo service tomcat7 restart



5. Adjust the settings in the "tk_props.txt" file.
---------------------------------------------------------------
    
We will now adjust the values in the /home/ubuntu/tttdir/external_cache/tk_props.txt
file for your local environment. The tool is setup to use Gmail for sending mail.
These instructions assume this configuration. Place your hostname where you see
"ttt.your-domain.com", your gmail password where you see "your-password", and your
Gmail username where you see "your-email".
    
    cd tttdir/external_cache
    sed -i -e 's/ttt.example.com/ttt.your-domain.com/g' tk_props.txt
    sed -i -e 's/change-to-your-password/your-gmail-password/g' tk_props.txt
    sed -i -e 's/change-to-your-direct-testing-email@gmail.com/your-email@gmail.com/g' tk_props.txt


6. Download and Install the ttt war 
-----------------------------------

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.your-domain.com
    
Download the ttt war and the listner scripts:


    wget http://sourceforge.net/projects/iheos/files/TransportTestingTool/version-160/ttt.war/download -O ttt.war
        

Install the ttt war file:

    sudo cp ttt.war /var/lib/tomcat7/webapps/

Note that the TTT will not work at this point without further configuration.
Complete all the step to get things working.

Adjust the default values in /var/lib/tomcat7/webapps/ttt/WEB-INF/toolkit.properties:
    
    cd  /var/lib/tomcat7/webapps/ttt/WEB-INF/

Change the name of the host to suit your environment, assuming yourt host name
is ttt.your-domain.com:

    sudo sed -i -e 's/example.com/ttt.your-domain.com/g' toolkit.properties
    
Change the default password.  Change "your-password" to your desired password.:

    sudo sed -i -e 's/easy/your-password/g' toolkit.properties

Now restart Tomcat

    sudo service tomcat7 restart

7. Create/Obtain certificates for your Domain 
---------------------------------------------

You will need to create:
   1. a Trust anchor CA, we will name "root". You will need the file "roo.der".
   2. A domain-bound certificates, buit from the aformentioned "root.", for
   "ttt.your-domain.com". You will need the files "ttt.your-domain.com.p12" and
   "ttt.your-domain.com.der".
   3.  Another trust anchor to serve as an untrusted anchor. We will call this
   "invalid-trust-relationship", and you will need the file
   "invalid-trust-relationship.der". This anchor is valid, but if you use it in
   conjunction with "ttt.your-domain.com" from #2, then it would be invalis because
   the domain-bound certifcate was not created with this trust anchor.
   
To generate these files, we will use the tool "certGen.sh" that is bundled with
the Java Direct RI.  There is a direct

The following link describes how you can go about doing this using the certGen
tool that comes bundled with Java Direct Reference Implementation (RI)

See https://github.com/meaningfuluse/mu2/blob/master/transport/creating-certificates-for-ttt.md

for how to complete this step.

8. Install Certificates and Properties file in the TTT
----------------------------------

After these files are created and transfered to the server, you will want to
place them in the correct location. Assuming that your trust anchor is "root.der",
your public certificate is "ttt.your-domain.com.der", and your domain level signing
certificate is "ttt.your-domain.com.p12", the copy these files to their required
location. assuming these files are your current directory, then execute the
following commands.

    sudo cp root.der /var/lib/tomcat7/webapps/ttt/pubcert/
    sudo cp ttt.your-domain.com.der /var/lib/tomcat7/webapps/ttt/pubcert/
    sudo cp invalid-trust-relationship.der /var/lib/tomcat7/webapps/ttt/pubcert/
    cp ttt.your-domain.com.der  ~/tttdir/external_cache/direct/encrypt_certs
    sudo cp ttt.your-domain.com.p12 /var/lib/tomcat7/webapps/ttt/WEB-INF/privcert/
    cp ttt.your-domain.com.p12 ~/tttdir/external_cache/direct/signing_cert/
    touch ~/tttdir/external_cache/direct/signing_cert/password.txt
    cd /var/lib/tomcat7/webapps/ttt/WEB-INF/privcert/
    sudo rm mykeystore.p12
    sudo chown tomcat7 *; sudo chgrp tomcat7 *
    
    
Set the file names in tk_props to the filenames used above. We are assuming that
your public cert is named "ttt.your-domain.com.der" and your trust anchor is
"root.der".  Change these to match the name of your files.
    
    cd ~/tttdir/external_cache
    sed -i -e 's/my-public-cert.der/ttt.your-domain.com.der/g' tk_props.txt
    sed -i -e 's/my-trust-anchor.der/root.der/g' tk_props.txt
    sed -i -e 's/my-invtrustrel.der/invalid-trust-relationship.der/g' tk_props.txt

Change the owndership and permissions on the external_cache and logs directory

    cd ~/tttdir/
    sudo chmod -R 777 external_cache/ logs/ bin/
    sudo chown -R tomcat7 external_cache/ logs/ bin/
    sudo chgrp -R tomcat7 external_cache/ logs/ bin/
    

You can test this step was completed successfully by going to running a script
that checks that all of the files are properly situated

    cd /var/lib/tomcat7/webapps/ttt/scripts
    bash cert-check.sh

9. Start the SMTP Daemon
------------------------

run the script that starts the SMTP listener

    cd ~/tttdir/bin
    sudo ./listener.sh start
    
This will start the SMTP lister on port 25.  You must use sudo to do this
because of the low port number.
    
To stop the listener, use:

    sudo ./listener.sh stop
    
10. Create the output folder for Direct Validation Messages
----------------------------------------------------------

You need to create the folder where direct validation messages, sent by email,
will reside.

    sudo mkdir /var/lib/tomcat7/webapps/ROOT/direct/
    sudo chown -R tomcat7 /var/lib/tomcat7/webapps/ROOT/direct/
    sudo chgrp -R tomcat7 /var/lib/tomcat7/webapps/ROOT/direct/


11. Restart and Test the Server
------------------------------

Restart Tomcat.

    sudo service tomcat7 restart


See if the necessary services are up and running:

    nmap 127.0.0.1
    
You should see evidence of a running service ports 25 and 8080. You can now go
to "http://ttt.your-domain.com:8080/ttt" and all should be working.

Test the server by attempting to send and receive Direct messages to and from the
TTT.  Please note you will need to add/enable the trust anchor AND the
domain-bound certificate into your Direct implementation.  This will be specific to
what Direct server you are running.  For example, you may be using the
"Java Direct RI", the "Windows .NET RI", or your own.  For information on
setting this up using the Java Direct RI, please visit -
https://github.com/meaningfuluse/mu2/blob/master/transport/direct-hello-world.md

Also note that if you update the war file, you will need to adjust the values
in var/lib/tomcat7/webapps/ttt/WEB-INF/toolkit.properties again.

Alan Viars
@aviars



Appendix I - How to update the TTT release on a server
------------------------------
The steps above that are involved in updating a release of the TTT on a server are:
6. Download and Install the ttt war

8. Install Certificates in the TTT

11. Restart and Test the Server


For a more detailed explanation, please see below.
1. Replace the old TTT .war file with the new one.
2. Restart the server in order to install the file that you just copied. This will regenerate the WEB-INF directory for the web application.
3. Re-installing certificates in the TTT webapp directory can be done easily by using the script:

    /home/ubuntu/install-certs-from-default.sh

4. Copy file toolkit.properties to the WEB-INF directory. The default toolkit.properties file is stored under /home/ubuntu. 

    sudo cp /home/ubuntu/toolkit.properties /var/lib/tomcat7/webapps/ttt/WEB-INF/toolkit.properties

5. Restart and test the server as described in step 11 of the main section above.
