Transport Testing Tool Configuration
====================================

This document describes how to get the Trasnpport Testing Tool configured on
your own hardware or instance.

THIS DOCUMENT IS IS IN ACTIVE DEVELOPENT SO SOME DETAILS MAY BE INCOMPLETE OR INCORRECT.

Last updated: Janurary 22, 2013

Overview:
---------


The high-level tasks are as follows:

1. Setup a new server with Tomcat7 and a public facing IP address.
2. Create a host name and configure DNS for your host.
3. Open the appropriate firewall ports (TCP 22,25,8080).
4. Create an "external_cache" directory.
5. Download and adjust the settings in the "tk_props.txt" file.
6. Create/Obtain certificates for your domain and install them in the "external_cache".
7. Download the ttt war package and the SMTP listner scripts.
8. Install/Deploy the ttt.war.
9. Activate the SMTP listener.


1. Setup a new server with a public facing IP address:
------------------------------------------------------

Setup a new server with a public facing IP address.  We are using Ubuntu
12.04 64-bit Server Edition.  Specificaly, we are using the AWS AMI:
ami-3d4ff254. Log into the server and install tomcat7. We assume the user you
are going to use to login is "ubuntu" and this user has sudo priveleages.
This is consistent with Ubuntu's AWS EC2 instances.

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com
    

    sudo apt-get update
    sudo apt-get -y install git-core tomcat7 tomcat7-admin unzip


2. Create a host name and configure DNS for your host:
------------------------------------------------------


Setup a static ip and a domain name. Create a static IP and associate 
it with a domain name.

In this example, we will use the IP  "123.123.123.123" and the hostnme
"ttt.example.com".

Setup the A, CNAME as show below.
 
    Type:           Source:             Destination:
    -----------     ----------------    ---------------
    A Record        *.example.com       123.123.123.123	
    A Record	    example.com         123.123.123.123
    A Record	    ttt.example.com     123.123.123.123	
    CNAME Record    mail.example.com    example.com	
    CNAME Record    smtp.example.com    example.com	


Setup the MX (Mail) Records as shown below.

    mail.example.com	20
    smtp.example.com	10


3. Open the Appropriate Firewall Ports 
--------------------------------------

Open the following inbound ports on your firewall.

    TCP Port (Service)	Source
    22 (SSH)	        0.0.0.0/0	
    25 (SMTP)           0.0.0.0/0
    8080 (HTTP*)        0.0.0.0/0	


4. Create the external_cache Directory
--------------------------------------

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com

Create the following directory and subdirectories from your home directory:

    cd
    mkdir tttdir
    mkdir tttdir/bin
    mkdir tttdir/logs
    mkdir tttdir/external_cache
    mkdir tttdir/external_cache/direct
    mkdir tttdir/external_cache/direct/contact
    mkdir tttdir/external_cache/direct/direct
    mkdir tttdir/external_cache/direct/encrypt_certs
    mkdir tttdir/external_cache/direct/signing_cert
    mkdir tttdir/external_cache/direct/sendlog/
    mkdir tttdir/external_cache/direct/sendlog/DefaultDirectUser
    mkdir tttdir/external_cache/TestLogCache



5. Download and adjust the settings in the "tk_props.txt" file.
---------------------------------------------------------------

Fetch the sample tk_props.txt file.
    
    cd
    wget http://ttt-files.s3.amazonaws.com/tk_props.txt
    
We will now adjust the values in the file for your local environment.
The tool is setup to use Gmail for sending mail.  These instructions assume this
configuration. 


    sed -i -e 's/ttt.transparenthealth.org/ttt.example.com/g' tk_props.txt
    sed -i -e 's/change-to-your-password/your-password/g' tk_props.txt
    sed -i -e 's/change-to-your-direct-testing-email@gmail.com/your-email@example.com/g' tk_props.txt

Now move the file into the external_cache:

    mv tk_props.txt tttdir/external_cache


6. Create/Obtain certificates for your domain and install them in the "external_cache".
------------------------------------------------------------------------------------

You will need to create a root CA and a domain-bound certificates for "ttt.example.com".
The following link describes how you can go about doing this using the certGen
tool that comes bundled with Java Direct Reference Implementation (RI)

See https://github.com/meaningfuluse/mu2/blob/master/transport/creating-direct-certificates-using-vmware.md




7. Download the ttt war package and the SMTP listner scripts.
-------------------------------------------------------------

Log into the server:

    ssh -i .ec2/ttt.pem ubuntu@ttt.example.com
    cd

Download the ttt war and the listner scripts:


    wget http://ttt-files.s3.amazonaws.com/ttt.158.war
    
Get the listener scripts:

    wget http://ttt-files.s3.amazonaws.com/listener.sh
    wget http://ttt-files.s3.amazonaws.com/direct-listener.sh
    

Install the ttt war file.

    sudo cp ttt.158.war /var/lib/tomcat7/webapps/ttt.war

Copy the lostener scripts into their correct location. 
    


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


