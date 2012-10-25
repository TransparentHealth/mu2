Direct Hello World
==================

The goal of this document is to outline the steps to necessary to setting up one
or more instances of Direct RI (Java). This includes setting up two direct servers,
DNS, certificate creation, and configuration.  It is designed to fill in some of
the gaps with the online documentation.

This document is a work in progress.

In this tutorial we will use the domains direct.transparenthealth.org and
direct.microphr.com. Follow the steps for both domains.  

These instructions assume you are using Ubuntu 12.04 LTS 64-bit Server Edition,
OpenJDK 7, and Direct 1.3. (This should also work for 1.4)

If you are running this in Amazon EC2, you will need at least a "small" sized
server, but a medium is reccomended.  A "micro" can't handle the load.


Firewall and DNS Settings:
--------------------------

Ensure ports 22, 25, 53, 80, 443, 8080, 8081, are open for inbound traffic.

Add an MX record for your domain.  ex.


    TYPE    HOST NAME               PRIORITY
    ----    -------------------     --------
    MX      direct.microphr.com     20


Install Prerequsites:
----------------------

    sudo apt-get update
    sudo apt-get install ant unzip openjdk-7-jdk nmap

    
Setup JAVA_HOME:
----------------

    export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-amd64
    echo "export JAVA_HOME=$JAVA_HOME" >> ~/.bashrc
    export JAVA_OPTS="-Xmx256m -XX:MaxPermSize=256m"
    echo "export JAVA_OPTS=$JAVA_OPTS" >> ~/.bashrc
    source ~/.bashrc 


Verify Java 7 is Installed:
---------------------------
    
    java -version


Install Cryptographic Extensions:
---------------------------------
You must fetch this from Oracle's website.  Then you can follow the instructions 
below.

    unzip jce_policy-1_4_2.zip
    sudo cp jce/US_export_policy.jar $JAVA_HOME/jre/lib/security
    sudo cp jce/local_policy.jar $JAVA_HOME/jre/lib/security


Get Direct version 1.3:
-----------------------
    
    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/1.3.2/direct-project-stock-1.3.2.tar.gz

Unpack the Direct Project:
--------------------------
    
    tar -zxvf direct-project-stock-1.3.2.tar.gz

Setup More Environment Variables
--------------------------------

    export DIRECT_HOME=`pwd`/direct
    echo "export DIRECT_HOME=$DIRECT_HOME" >> ~/.bashrc
    source ~/.bashrc

Start Tomcat
------------
    
    cd $DIRECT_HOME/apache-tomcat-7.0.25/bin
    ./startup.sh

This process may take a few minutes to complete.  Verify it is working by pointing your browser to: 

    http://[servername]:8081/config-ui/

Create Certificates Using certGen.sh
------------------------------------

You will need to create a root CA trust anchor and "leaf nodes" for each email.
Note you will need to do this step on a "headed" server, because the certGen tool
has a Java-based graphical user interface (GUI).  For your convienece I've created
a VMWare Virtual Machine with this already setup. See
http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip

    cd $DIRECT_HOME/tools
    ./certGen.sh

Create a Root Trust anchor for each domain.  Example 1: direct. microphr.com

    CN:                 [Name for your CA - ex. "Root for Direct.MicroPHR.com"]
    Country:            [Your Country]
    State:              [Your State]
    Location:           [Your City]
    Org:                [Your Organization Name]
    Email:              [Email for Root CA - ex. root@direct.microphr.com]
    Experiation Days:   365
    Key Strength:       1024
    Password:           [Your password]

Press the "Create" button.  you will use the file "root.der", getting its name
from the user name part of the email provided, to serve as your Trust Anchor.


Now create some leaf certificates, by clicking "Create Leaf Cert".

    CN:                 [Name for user - ex. "alan"]
    Country:            [Your Country]
    State:              [Your State]
    Location:           [Your City]
    Org:                [Your Organization Name]
    Email:              [Email for user- ex. alan@direct.microphr.com]
    Experiation Days:   365
    Key Strength:       1024
    Password:           LEAVE BLANK
    Click Add Email to Alt subject Names

Click Create.  This will create 3 additional files. alan.der, alanKer.der, and
alan.p12.  The file "alan.der" is the public key used by others to send "alan"
private messages.  The file "alan.p12" is the private key used by "alan" to
digitally sign the email before it is sent.  This entire process is transparent
to the sender and receiver.  This is what Direct is all about.

Repeat the "Create Leaf Cert" process for any other users you want to create.
I've done this for "bill" as well as "alan".  I also completed the same for the
domain "direct.transparenthealth.org".

Now we need to add the trust anchor(s) and the email certs to the direct tool.

Setup Direct via the config-ui
------------------------------

1. Naviagate to: http://[servername]:8081/config-ui/
2. Login with user name "admin" and password "adm1nD1r3ct".
3. Click "Create a New Domain".
4. Add the domain name. ex. direct.mcrophr.com
5. Add the postmaster email address name should be "postmaster". ex. postmaster@direct.microphr.com
6. Status should be set to Enabled.
7. Click "Add".
8. Click on the Anchors tab.
9. Add the certificate file "root.der".
10. Check incomming, outgoing, and set the status to enabled.
11. Click add Anchor.

Note that if you want to share information between other domains, you need to
get their certificate and add it here.  Use the same options as above. In our
example, we will need to add the trust anchor "direct.transparenthealth.org".
We will also need to add the "direct.transparenthealth.org" and
"direct.microphr.com" anchors to the direct.transparenthealth.org server's
config-ui.  It is necessary for both domains to have swapped anchors to enable
bi-directional communication across the two domains.


Now click on "Agent Settings". Add the following Key Value settings.

    KEY                 VALUE
    ---------------     ------------------------
    AnchorStoreType     WS
    MDNAutoResponse     true
    PrivateStoreType    WS
    PublicStoreType     WS,DNS,PublicLDAP
    
    
Now click on Certificates.

1. Add the certificate "alan.der", select Enabled for status and click "Add Certificate."
2. Repeat this process for "alan.p12".
3. Repeat the process for other email users.  "bill.der" and "bill.p12".

Note that adding certs here means that Direct will work without your certificates
being discoverable in DNS or LDAP.  Adding them here makes them discoverable
via Web Services.  The Agent Setting "PublicStoreType", says "try the web service,
then DNS, and then LDAP.

Note that any time something changes here, you will need to shutdown and restart
the James email server.  Lets configure and fire up the James webserver now.


Setup James
-----------

    cd $DIRECT_HOME/james-2.3.2
    sh bin/setdomain.sh <your domain name>

Start James
-----------

    cd $DIRECT_HOME/james-2.3.2
    sudo -E sh bin/run.sh > james.log 2>&1 &
    
Create a few users on James
----------------------------

    telnet localhost 4555
    root
    root
    adduser bill password
    adduser alan password
    quit
    
Start the DNS Server
--------------------

Switch to the DNS folder.

    cd $DIRECT_HOME/DirectDNSServices/DirectDNSServer/bin

On a 64-bit system there is a bug that prevents the server from strarting.
USe this workaround.

    mv wrapper-linux-x86-32  wrapper-linux-x86-32.bak
    cp wrapper-linux-x86-64 wrapper-linux-x86-32
    sudo  ./DirectDNSServer start
    

Verify Things are Working:
--------------------------
    
    nmap 127.0.0.1
    
Note it might take a few minutes for the services to come online.


You should see this:

    PORT     STATE SERVICE
    22/tcp   open  ssh
    25/tcp   open  smtp
    53/tcp   open  domain
    110/tcp  open  pop3
    119/tcp  open  nntp
    8009/tcp open  ajp13
    8081/tcp open  blackice-icecap


Sending an Email from Bill to Alan
----------------------------------

At this point you should be able to send a Direct email between bill and alan

You can use Thunderbird or any POP3/SMTP client to achieve this.
Just add the accounts with the following information.

User 1:
~~~~~~~

    user:               alan
    password:           password 
    pop3 server:        direct.microphr.com
    smtp server:        direct.microphr.com


User 2:
~~~~~~~

    user:               bill
    password:           password 
    pop3 server:        direct.microphr.com
    smtp server:        direct.microphr.com

    
User 3:
~~~~~~~

    user:               alan
    password:           password 
    pop3 server:        direct.transparenthealth.org
    smtp server:        direct.transparenthealth.org


User 4:
~~~~~~~

    user:               bill
    password:           password 
    pop3 server:        direct.transparenthealth.org
    smtp server:        direct.transparenthealth.org



Configuring DNS:
----------------

The next step is to setup and configure the DNS server to server certificates.
This can supposedly be accomplished with BIND, but I have not found any
instructions on how to do this.  We will use the "DirectDNSServer" we just setup.

TODO.....

