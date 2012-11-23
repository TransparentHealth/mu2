Transport Testing Tool Configuration
====================================

This document describes how to get the Trasnpport Testing Tool configured on
your own hardware or instance.

THIS DOCUMENT IS IS IN ACTIVE DEVELOPENT SO SOME DETAILS MAY BE INCOMPLETE OR INCORRECT.

Nov. 19 2012.

Overview:
---------


1. Assign DNS domain 

    + Register domain
    + Establish MX records etc. in DNS environment
    + Update tk_props.txt configuration file

2. SMPT server for sending validation reports
     + Validation report message format (local installation can have customized wording)
     + Direct (To) addresses for triggering CCDA validation
     + DNS domain where toolkit is installed

3. Create and install encryption cert tied to this domain

    + Create and install trust anchor tied to this domain (or find existing trust anchor is this site is covered within its namespace)
    + Create and install signing cert tied to this domain
    + Configure Ubuntu Linux (under VMWare) with hostname/IP address taken from this DNS domain
    + Establish configuration for DNS servers
    
4. Create and install certificate for TLS (assuming use of SOAP elements of toolkit) 

    + TBD co


Firewall Configuration
----------------------

Open the following inbound ports.

    TCP Port (Service)	Source
    22 (SSH)	        0.0.0.0/0	
    25 (SMTP)           0.0.0.0/0
    8080 (HTTP*)        0.0.0.0/0	


DNS Configuration
-----------------

Setup a static ip and a domain name. Create a static IP and associate 
it with a domain name.

In this example, we will use the IP  "123.123.123.123" and the hostnme
"example.com".

Setup the A, CNAME as show below.
 
    Type:           Source:             Destination:
    -----------     ----------------    ---------------
    A Record        *.example.com       123.123.123.123	
    A Record	    example.com         123.123.123.123	
    CNAME Record    mail.example.com    example.com	
    CNAME Record    smtp.example.com    example.com	


Setup the MX (Mail) Records as shown below.

    mail.example.com	20
    smtp.example.com	10
    


  


Generating Certificates
-----------------------

See https://github.com/meaningfuluse/mu2/blob/master/transport/creating-direct-certificates-using-vmware.md



Installing from Scratch (Unsupported)
-------------------------------------

*Fetch the Necessary Prerequisites:*


    sudo apt-get update
    sudo apt-get install git-core openjdk-7-jdk
    wget http://mirrors.ibiblio.org/apache/tomcat/tomcat-5/v5.5.36/bin/apache-tomcat-5.5.36.tar.gz
    tar  -zxvf apache-tomcat-5.5.36.tar.gz
    wget http://foo.com/yourcerts.tar.gz
    tar -zxvf yourcerts.tar.gz
    cp yourcerts/* /home/ubuntu/apache-tomcat-5.5.36/webapps/ttt/pubcert

*Setup JAVA_HOME and CATALINA_HOME:*


    export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-amd64
    echo "export JAVA_HOME=$JAVA_HOME" >> ~/.bashrc
    export JAVA_OPTS="-Xmx256m -XX:MaxPermSize=256m"
    echo "export JAVA_OPTS=$JAVA_OPTS" >> ~/.bashrc
    export CATALINA_HOME=/home/ubuntu/apache-tomcat-5.5.36
    echo "export CATALINA_HOME=$CATALINA_HOME" >> ~/.bashrc
    source ~/.bashrc 


*Verify Java 7 is Installed:*
    
    java -version


*Install Cryptographic Extensions:*

You must fetch the "jce_policy-1_4_2.zip" file from Oracle's website.  Then you
can follow the instructions below.

    unzip jce_policy-1_4_2.zip
    sudo cp jce/US_export_policy.jar $JAVA_HOME/jre/lib/security
    sudo cp jce/local_policy.jar $JAVA_HOME/jre/lib/security
    

*Start the Tomcat Server*

Not you will most likely need to adjust the configuration file before completing
the next step.

    cp apache-tomcat-5.5.36/bin
    sudo ./startup.sh





Setting up the Configuration File
---------------------------------

Adjust the configuration file for your site. (TODO..Add detailed instructions here.)


The TTT configuration file is found.



    # Select the home page displayed. If commented out, everything
    # will be shown.  To show just the Direct project then code
    # toolkit.home: direct
    toolkit.home: direct
    
    direct.reporting.privKeyPassword: 
    
    # Mailer definition for for service used to send announcements
    # of reports that are available
    # to Contact email addresses
    #direct.reporting.mail.starttls.enable: true
    direct.reporting.mail.host: spamav1.nist.gov
    direct.reporting.mail.from: direct-testing@hit-testing.nist.gov
    direct.reporting.mail.smtp.port: 25
    direct.reporting.mail.smtp.auth: 
    direct.reporting.mail.smtp.starttls: false 
    direct.reporting.mail.smtp.user: direct-testing
    direct.reporting.mail.smtp.password: xxxxxxxxxxx
    direct.reporting.mail.debug: false
    
    # smtp or smtps
    direct.reporting.mail.transport: smtp
    
    # Send announcement email?
    direct.reporting.announce:true
    
    # Where the reports are stored
    direct.reporting.directory: /home/bill/ttt/webapps/ROOT/direct/
    direct.reporting.baseurl: http://hit-testing.nist.gov:9100/direct/
    
    # Message template for announcements
    direct.reporting.validation.template: Validation report available from
    direct.reporting.validation.template: %validation.url%
    direct.reporting.validation.template: Through later
    
    # Mapping from Direct (To) address and CCDA Document Type
    # The type must align with the toolkit software
    # the display is not processed, only displayed
    # the directTo addr must align with the type in the software
    # Entries with all 3 params present will be displayed on the TTT sign-up page
    # Entries with type and display present will be displayed on the Message Validator page
    
    # The entries will be displayed in the order indicated (*n)
    direct.reporting.ccdatype.type1: Clinical Office Visit Summary
    direct.reporting.ccdatype.display1: MU2 170.314(e)(2) - Clinical Summary
    direct.reporting.ccdatype.directTo1: direct-clinical-summary@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type2: Transitions Of Care Ambulatory Summary
    direct.reporting.ccdatype.display2: MU2 170.314(b)(2) Transition of Care/Referral Summary - For Ambulatory Care
    direct.reporting.ccdatype.directTo2: direct-ambulatory@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type3: Transitions Of Care Ambulatory Summary
    direct.reporting.ccdatype.display3: MU2 170.314(b)(7) Data Portability - For Ambulatory Care
    direct.reporting.ccdatype.directTo3: direct-ambulatory@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type4: Transitions Of Care Ambulatory Summary
    direct.reporting.ccdatype.display4: MU 2 170.314(b)(1) Transition of Care Receive – For Ambulatory Care
    direct.reporting.ccdatype.directTo4: direct-ambulatory@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type5: Transitions Of Care Inpatient Summary
    direct.reporting.ccdatype.display5: MU2 170.314(b)(2) Transition of Care/Referral Summary - For Inpatient Care
    direct.reporting.ccdatype.directTo5: direct-inpatient@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type6: Transitions Of Care Inpatient Summary
    direct.reporting.ccdatype.display6: MU2 170.314(b)(7) Data Portability - For Inpatient Care
    direct.reporting.ccdatype.directTo6: direct-inpatient@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type7: Transitions Of Care Inpatient Summary
    direct.reporting.ccdatype.display7: MU 2 170.314(b)(1) Transition of Care Receive – For Inpatient Care
    direct.reporting.ccdatype.directTo7: direct-inpatient@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type8: VDT Ambulatory Summary
    direct.reporting.ccdatype.display8: MU2 170.314 (e)(1) Ambulatory Summary
    direct.reporting.ccdatype.directTo8: direct-vdt-ambulatory@hit-testing.nist.gov
    
    direct.reporting.ccdatype.type9: VDT Inpatient Summary
    direct.reporting.ccdatype.display9: MU2 170.314 (e)(1) Inpatient Summary
    direct.reporting.ccdatype.directTo9: direct-vdt-inpatient@hit-testing.nist.gov


