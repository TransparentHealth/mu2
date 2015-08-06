Direct Hello World
==================

The goal of this document is to outline the steps to necessary to setting up one
or more instances of Direct RI (Java). This includes setting up two direct servers,
DNS, certificate creation, and configuration.  It is designed to fill in some of
the gaps with the online documentation.

This document is a work in progress.

In this tutorial we will use the domains direct.transparenthealth.org using
Direct RI 1.3 and direct.microphr.com using Direct RI 2.0.  I will point out
the variations  in configration between 1.3, 1.4, and 2.0 where applicable. 
  

These instructions assume you are using Ubuntu 12.04 LTS 64-bit Server Edition,
OpenJDK 7.

If you are running this in Amazon EC2, you will need at least a "small" sized
server, but a medium is reccomended.  A "micro" can't handle the load.


Firewall and DNS Settings:
--------------------------

Ensure ports 22, 25, 53, 80, 8081, are open for inbound traffic.

    PORT    TYPE    FUNCTION
    ----    ----    -------------------------------------
    22      TCP      ssh
    25      TCP      SMTP (Unencrypted)
    53      TCP      DNS
    53      UDP      DNS
    80      TCP      HTTP (not absolutley needed unless you run Apache, Squirrel mail, etc.
    110     TCP      POP3
    8081    TCP      HTTP Tomcat. Give access to http://yourhost:8081/config-ui


Install Prerequsites:
----------------------

    sudo apt-get update
    sudo apt-get -y install ant unzip openjdk-7-jdk nmap ant


Setup JAVA_HOME:
----------------

    export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-amd64
    echo "export JAVA_HOME=$JAVA_HOME" >> ~/.bashrc
    export JAVA_OPTS="-Xmx256m -XX:MaxPermSize=256m"
    echo "export JAVA_OPTS=\"$JAVA_OPTS\"" >> ~/.bashrc
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


Get Direct version :
--------------------

Download the version of direct you want to use.  We are using both 1.3 and 2.0
in this tutorial but if you are just starting I'd reccomend using 3.0.1.

3.0.1

    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/3.0.1/direct-project-stock-3.0.1.tar.gz
    
2.0

    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/2.0/direct-project-stock-2.0.tar.gz

1.4
    
    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/1.4/direct-project-stock-1.4.tar.gz
    
1.3

    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/1.3.2/direct-project-stock-1.3.2.tar.gz


Unpack the Direct Project:
--------------------------

Unpack your chosen version of direct.

    tar -zxvf direct-project-stock-2.0.tar.gz

Setup More Environment Variables
--------------------------------

    export DIRECT_HOME=`pwd`/direct
    echo "export DIRECT_HOME=$DIRECT_HOME" >> ~/.bashrc
    source ~/.bashrc

Start Tomcat
------------

    cd $DIRECT_HOME/apache-tomcat-7.0.32/bin
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

Create a Root Trust anchor for each domain.  Example 1: direct.microphr.com

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

Click Create.  This will create 3 additional files. alan.der, alanKey.der, and
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
4. Add the domain name. ex. direct.microphr.com
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
the James email server in version 1.3, but should not have to do this in 1.4 or 2.0. As of version 1.4, these agent setting are refreshed from the configuration service every 5 minutes.  So unless you need the changes to take affect immediately, James does not need to be restarted.


Lets configure and fire up the James email server now.


Setup James
-----------

Note that `ant` should be installed and on your `$PATH`.

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

    user:               alan
    password:           password
    pop3 server:        direct.microphr.com
    smtp server:        direct.microphr.com


User 2:

    user:               bill
    password:           password
    pop3 server:        direct.microphr.com
    smtp server:        direct.microphr.com


User 3:

    user:               alan
    password:           password
    pop3 server:        direct.transparenthealth.org
    smtp server:        direct.transparenthealth.org


User 4:

    user:               bill
    password:           password
    pop3 server:        direct.transparenthealth.org
    smtp server:        direct.transparenthealth.org


Configuring DNS:
----------------

The next step is to setup and configure the DNS server to server certificates.
I've read it be accomplished with BIND, but I have not found any
instructions on how to do this.  We will use the "DirectDNSServer" we just
setup in the previous step.


 
Goto http://yourhost:8081/config-ui and log in.  Now click "DNS Entries" and
create the following entires below: Replace "123.123.123.123" with the IP
address of your Direct RI instance.

A Records:

    Name                  IP Address         TTL
    ====================  ===============    =====
    microphr.com.         123.123.123.123    86400 	
    direct.microphr.com.  123.123.123.123    86400 	
    www.microphr.com. 	  123.123.123.123    86400 	
    ns1.microphr.com. 	  123.123.123.123    86400 	
    ns2.microphr.com. 	  123.123.123.123    86400 	
    pop3.microphr.com. 	  123.123.123.123    86400 	
    smtp.microphr.com. 	  123.123.123.123    86400 	
    mail.microphr.com. 	  123.123.123.123    86400 	

MX (Mail Exchange) Records:

    Priority 	Host                    Alias For          TTL
    ========    ====================    ===============    =====
    0 	        direct.microphr.com. 	123.123.123.123.   86400


"SOA" (Start of Authority) Records:


    Name                  Host Master               Name Server         TTL     Expire  Min.  Refresh  Serial  Retry
    ====================  ========================  =================   =====   ======  ====  =======  ======  =====
    direct.microphr.com.  postmaster.microphr.com.  ns1.microphr.com. 	86400 	0 	0     0        0 0


"NS" Records:

    Name                  Target                    TTL
    =============         =================         =====
    microphr.com.         ns1.microphr.com.         86400 	
    microphr.com.         ns2.microphr.com.         86400 	


The next step is to make sure that the domain you are using uses the DNS server
we just configured.  You may need to ask you hosting provider to setup some custom
routes before you are able to point your domain's name servers to the DNS server.
This step could take a few hours up to a day or two to complete.

Go to your domain hosting providers website and setup the NS records to point to
"ns1.microphr.com" and "ns1.microphr.com".

You can verify this step is completed by using whois.

    whois microphr.com

You should see this in the output.

    Name Server.......... ns1.microphr.com
    Name Server.......... ns2.microphr.com



Now the public certificates you setup are accessiable via DNS.  You can test
this with dig.

    dig alan.direct.microphr.com CERT

will return

    ;; Truncated, retrying in TCP mode.

    ; <<>> DiG 9.8.1-P1 <<>> alan.direct.microphr.com CERT
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 42760
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;alan.direct.microphr.com.	IN	CERT

    ;; ANSWER SECTION:
    alan.direct.microphr.com. 67179	IN	CERT	PKIX 33535 RSASHA1 MIID
    qzCCAxSgAwIBAgIIInSMiuOvJhMwDQYJKoZIhvcNAQEFBQAwgZIx JzAlBgkqhkiG9w0BCQEWGH
    Jvb3RAZGlyZWN0Lm1pY3JvcGhyLmNvbTEl MCMGA1UEAwwcUm9vdCBmb3IgRGlyZWN0Lk1pY3Jv
    UEhSLmNvbTEMMAoG A1UEBhMDVVNBMQswCQYDVQQIDAJNRDESMBAGA1UEBwwJQmFsdGltb3Jl M
    REwDwYDVQQKDAhNaWNyb1BIUjAeFw0xMjExMDIxNjI3MDdaFw0xMzEx MDIxNjI3MDdaMHoxJzA
    lBgkqhkiG9w0BCQEWGGFsYW5AZGlyZWN0Lm1p Y3JvcGhyLmNvbTENMAsGA1UEAwwEYWxhbjEMM
    AoGA1UEBhMDVVNBMQsw CQYDVQQIDAJNRDESMBAGA1UEBwwJQmFsdGltb3JlMREwDwYDVQQKDAh
    N aWNyb1BIUjCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEAhViwReTQ uvOyDEAYPziSfRej
    cwrRzPSVgYJ42ievj60sGEujStPWL+6DGJa1AQxr PPGKEchfbIYmgWK44Hi9tAQnh/yDnsSS+m
    ziF4oqMvAOHhyIdD+kezoE cCkgeKwX+9sH7I6xfmmXRyHTMgY7aLZMPD8pXOS80bAbLUpygv8CA
    wEA AaOCAR8wggEbMIHGBgNVHSMEgb4wgbuAFEJDEQ/nTrjjVmOya9ROO0G7 joNsoYGYpIGVMIG
    SMScwJQYJKoZIhvcNAQkBFhhyb290QGRpcmVjdC5t aWNyb3Boci5jb20xJTAjBgNVBAMMHFJvb3
    QgZm9yIERpcmVjdC5NaWNy b1BIUi5jb20xDDAKBgNVBAYTA1VTQTELMAkGA1UECAwCTUQxEjAQB
    gNV BAcMCUJhbHRpbW9yZTERMA8GA1UECgwITWljcm9QSFKCCHCr+CmYYfnI MB0GA1UdDgQWBBS
    nzDyrGrsCnH+euWZBv5+v9QYNBDAMBgNVHRMBAf8E AjAAMCMGA1UdEQQcMBqBGGFsYW5AZGlyZW
    N0Lm1pY3JvcGhyLmNvbTAN BgkqhkiG9w0BAQUFAAOBgQDbKh89WU56U6NRA3KWeov9vg5EeyB36
    IM/ txzaMxhB8zYv/yU8/RNZwHUCB8H6pB9KlakSZXDPOLuk/7S9gqR48rDR 0aCLliYuNMKnE26
    vgRyyR1tE6xKqkuHvyJOLqXoZXaW6qCCuqqpPRu4p prsCG+3ixwT8gUlaTkswAhvvOg==

    ;; Query time: 1 msec
    ;; SERVER: 172.16.0.23#53(172.16.0.23)
    ;; WHEN: Mon Nov 12 19:34:10 2012
    ;; MSG SIZE  rcvd: 1002

You now have a fully functioning version of Direct RI.  The following steps are optional but probally a good idea.  They have only been tested on Direct RI 2.0.


Deliver MDNs to the Edge Client
===============================

In the file `$DIRECT_HOME/james-2.3.2/apps/james/SAR-INF/config.xml` find the line `<ConsumeMDNProcessed>true</ConsumeMDNProcessed>` and change the faclue from `true` to `false`. Restart James.



Change the Password on the RI
=============================

Issue the following commands to backup the original file and find/place the password.


    cp $DIRECT_HOME/apache-tomcat-7.0.32/webapps/config-ui/WEB-INF/config-servlet.xml $DIRECT_HOME/apache-tomcat-7.0.32/webapps/config-ui/WEB-INF/config-servlet.xml.orig
    sed -i "s/adm1nD1r3ct/your_new_password/g" $DIRECT_HOME/apache-tomcat-7.0.32/webapps/config-ui/WEB-INF/config-servlet.xml
    cd $DIRECT_HOME/apache-tomcat-7.0.32
    sh bin/shutdown.sh
    sh bin/startup.sh



Optional Setup Steps
====================

Setting up James 3, IMAP, TTL, and SquirrelMail (TODO)


Other Notes:
============


Fixing .p12 bug in 4.0
----------------------
In 4.0 there is a bug related to .p12 files.  To fix do the following

* Shutdown James and Tomcat and perform the following
* `cd   $DIRECT_HOME/apache-tomcat-7.0.59/webapps/config-service/WEB-INF/lib` 
*`rm `config-store-1.5.1.jar`
* `wget https://oss.sonatype.org/content/repositories/snapshots/org/nhind/config-store/1.5.1-SNAPSHOT/config-store-1.5.1-20150730.130206-1.jar`
* `cd $DIRECT_HOME`
* `mv  nhindconfig nhindconfig-bak`
* `$DIRECT_HOME/apache-tomcat-7.0.59/bin/startup.sh

Configuring MySQL or Oracle:
----------------------------

Change database settings by editing the file `$DIRECT_HOME/apache-tomcat-7.0.59/webapps/config-service/WEB-INF/beans.xml`. By default Derby is used. If MySQL is used a DB must be setup and a DB user created.  The MySQL Jars must also be added as they are not bundled with the RI.



