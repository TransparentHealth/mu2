Transport Testing Tool Configuration
====================================

This document describes how to get the Trasnpport Testing Tool configured on
your own hardware or instance.

THIS DOCUMENT IS IS IN ACTIVE DEVELOPENT SO SOME DETAILS MAY BE INCOMPLETE OR INCORRECT.

Last updated: February 1, 2013

Overview:
---------


You MUST use Ubuntu 12.04 LTS 64-bit Server Edition.  No other operating systems
are supported at this time.

The high-level tasks are as follows:

1. Setup a new server with Tomcat7 and a public facing IP address.
2. Create a host name and configure DNS for your host.
3. Open the appropriate firewall ports (TCP 22,25,8080).
4. Download the decompress the"tttdir" package.
5. Adjust the settings in the "tk_props.txt" file.
6. Download and Install the ttt war 
7. Create/Obtain certificates for your domain and install them in the "external_cache".
8. Start the SMTP Daemon
9. Test the Server


1. Setup a new server with a public facing IP address:
------------------------------------------------------

Setup a new server with a public facing IP address.  We are using Ubuntu
12.04 64-bit Server Edition.  Specificaly, we are using the AWS AMI:
ami-3d4ff254. Log into the server and install tomcat7. We assume the user you
are going to use to login is "ubuntu" and this user has sudo priveleages.
This is consistent with Ubuntu's AWS EC2 instances.

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com
    sudo apt-get update
    sudo apt-get -y install tomcat7 tomcat7-admin nmap


2. Create a host name and configure DNS for your host:
------------------------------------------------------

Setup a static ip and a domain name. Create a static IP and associate  it with
a domain name. In this example, we will use the IP  "123.123.123.123" and the
hostnme "ttt.example.com".

Setup the A, CNAME as show below.
 
    Type:           Source:             Destination:
    -----------     ----------------    ---------------
    A Record        *.example.com       123.123.123.123	
    A Record	    example.com         123.123.123.123
    A Record	    ttt.example.com     123.123.123.123	
    MX Record       mail.example.com    123.123.123.123
    MX Record       smtp.example.com    123.123.123.123	
    MX Record       example.com         123.123.123.123	


3. Open the Appropriate Firewall Ports 
--------------------------------------

Open the following inbound ports on your firewall.

    TCP Port (Service)	Source
    22 (SSH)	        0.0.0.0/0	
    25 (SMTP)           0.0.0.0/0
    8080 (HTTP*)        0.0.0.0/0	


4. Download the decompress the"tttdir" package.
-----------------------------------------------

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com

Create the following directory and subdirectories from your home directory:

    wget http://ttt-files.s3.amazonaws.com/tttdir-2012-02-01.tar.gz
    tar zxvf tttdir-2012-02-01.tar.gz


5. Adjust the settings in the "tk_props.txt" file.
---------------------------------------------------------------
    
We will now adjust the values in the /home/ubuntu/tttdir/external_cache/tk_props.txt
file for your local environment. The tool is setup to use Gmail for sending mail.
These instructions assume this configuration. Place your hostname where you see
"ttt.example.com", your gmail password where you see "your-password", and your
Gmail username where you see "your-email".
    
    cd tttdir/external_cache
    sed -i -e 's/ttt.transparenthealth.org/ttt.example.com/g' tk_props.txt
    sed -i -e 's/change-to-your-password/your-password/g' tk_props.txt
    sed -i -e 's/change-to-your-direct-testing-email@gmail.com/your-email@gmail.com/g' tk_props.txt



6. Download and Install the ttt war 
-----------------------------------

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com
    
Download the ttt war and the listner scripts:


    wget http://ttt-files.s3.amazonaws.com/ttt.158b.war
        

Install the ttt war file.

    sudo cp ttt.158b.war /var/lib/tomcat7/webapps/ttt.war

Note that the TTT will not work at this point without further configuration.
Complete all the step to get the system installed.

Adjust the default values in /var/lib/tomcat7/webapps/ttt/WEB-INF/toolkit.properties.
    
    cd  /var/lib/tomcat7/webapps/ttt/WEB-INF/

Change the location of external_cache

    sudo sed -i -e 's/\/Users\/gerardin\/IHE-Testing\/xdstools2_environment/\/home\/ubuntu\/tttdir\/external_cache\//g' toolkit.properties

Change the default password

    sudo sed -i -e 's/money/your-password/g' toolkit.properties


7. Create/Obtain certificates for your domain and install them in the "external_cache".
------------------------------------------------------------------------------------

You will need to create a root CA and a domain-bound certificates for "ttt.example.com".
The following link describes how you can go about doing this using the certGen
tool that comes bundled with Java Direct Reference Implementation (RI)

See https://github.com/meaningfuluse/mu2/blob/master/transport/creating-direct-certificates-using-vmware.md

After these files are created and transfered to the server, you will want to
place them in the correct location. Assuming that your trust anchor is "root.der",
your public certificate is "ttt.example.com.der", and your domain level signing
certificate is "ttt.example.com.p12", the copy these files to their required
location. assuming these files are your current directory, then execute the
following commands.

    sudo cp root.der /var/lib/tomcat7/webapps/ttt/pubcert/
    sudo cp ttt.example.com.der  /var/lib/tomcat7/webapps/ttt/pubcert/
    cp ttt.example.com.p12 ~/tttdir/external_cache/direct/signing_cert
    
    
Set the file names in tk_props to the filenames used above.
    
    cd tttdir/external_cache
    sed -i -e 's/my-public-cert.der/ttt.example.com.der/g' tk_props.txt
    sed -i -e 's/my-trust-anchor.der/root.der/g' tk_props.txt


Change the owndership and permissions on the ttt dir

    sudo chmod -R 777 tttdir
    sudo chown -R tomcat7 tttdir
    sudo chgrp -R tomcat7 tttdir

8. Start the SMTP Daemon
------------------------

run the script that starts the SMTP listener

    cd ~/tttdir/bin
    sudo ./listener.sh start
    
This will start the SMTP lister on port 25.  You must use sudo to do this
because of the low port number.
    
To stop the listener, use:

    sudo ./listener.sh stop
    

9. Test the Server
------------------

See if the necessary services are up and running:


    nmap 127.0.0.1
    You should see evidence of a running service ports 25 and  8080.
    
You can now go to "http://ttt.example.com:8080/ttt" and all should be working.

Please note that if you update the war file, you will need to adjust the values
in var/lib/tomcat7/webapps/ttt/WEB-INF/toolkit.properties again.

Alan

