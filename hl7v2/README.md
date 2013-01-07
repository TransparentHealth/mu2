NIST HL7 v2 Tools
=================

These test tools include Electronic Immunization, Lab Results Interface (LRI), Lab Reporting (ELR), 
and Syndromic.

Below are links to the links, installation information, and other related information. 


Immunization Test Tool
----------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL -  (http://hl7v2-iz-testing.nist.gov)
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-immunization-1.0.0.war

Lab Results Interface (LRI) Test Tool
-------------------------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-lab-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-lab-1.0.0.war


Electronic Lab Reporting (ELR) Test Tool
-----------------------------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-elr-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-elr-1.0.0.war

Syndromic Test Tool
-------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-ss-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-syndromic-1.0.0.war


Ubuntu Linux Installation Instructions
--------------------------------------

These instructions assume an Ubuntu 12.04 LTS 64bit Server edition. 
Open up a terminal window and type:


For HL7v2 tools:

    sudo apt-get update
    
    #Installing Tomcat7 will also install OpenJDK 1.6
    sudo apt-get -y install tomcat7 tomcat7-admin unzip
    
    #Fetch the WAR archives from subversion
    wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-lab-1.0.0.war
    wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-elr-1.0.0.war
    wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-syndromic-1.0.0.war
    wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-immunization-1.0.0.war
 
    #Copy the War files the webapps folder for deployment
    sudo cp mu-lab-1.0.0.war /var/lib/tomcat7/webapps/
    sudo cp mu-elr-1.0.0.war /var/lib/tomcat7/webapps/
    sudo cp mu-syndromic-1.0.0.war /var/lib/tomcat7/webapps/
    sudo cp mu-immunization-1.0.0.war /var/lib/tomcat7/webapps/

For ePrescribing:
    
    #Installing Tomcat7 will also install OpenJDK 1.6
    sudo apt-get install tomcat7 tomcat7-admin unzip

    #Fetch the WAR archives from subversion
    wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-eprescribing-1.0.0.zip

    #Unzip the zip file containing both EDI and XML eRx War files
    unzip mu-eprescribing-1.0.0.zip

    #Copy the War files the webapps folder for deployment
    sudo cp mu-eprescribing-xml.war /var/lib/tomcat7/webapps/
    sudo cp mu-eprescribing-edi.war /var/lib/tomcat7/webapps/
  

If your server's IP is "127.0.0.1", then you can access the tools at the following URLS.

+ http://127.0.0.1:8080/mu-elr-1.0.0
+ http://127.0.0.1:8080/mu-syndromic-1.0.0
+ http://127.0.0.1:8080/mu-lab-1.0.0
+ http://127.0.0.1:8080/mu-immunization-1.0.0
+ http://127.0.0.1:8080/mu-eprescribing-xml
+ http://127.0.0.1:8080/mu-eprescribing-edi
 

Windows Installation Instructions
---------------------------------

Windows instructions can be found inside the tool itself.  From within the HL7v2 tool.
Hint: See the NIST Testing Server URL's decribed in the first section.

From within the tool, Click on the Documentation tab.  Then naviagate to the "Resource 
Documentation-->Validation Tool Download" section.  You will see a link to download Windows 
installation instructions.


Amazon Machine Images (AMI)
---------------------------

The AMI is based on Ubuntu 12.04 64-bit and has Apache Tomcat 7 and the 
projects are already installed. It is based on the instructions described in
"Ubuntu Linux Installation Instructions" section.

*AMI ID:* ami-f54fc69c

*AMI Name:* nist-hl7v2-erx-1.0.0

*Description:* NIST HL7v2 and ePrescribing Test Tools version 1.0.0. Runs atop Ubuntu 12.04 LTS, Tomcat7, OpenJDK 1.6.

Notes:  Use at least a "small" instance size. Be sure to follow the firewall instructions in this document for your instance. When connecting to the host via ssh, remember that the default user name is "ubuntu" and 
not "root".  Be sure and "chmod 400 your.pem", where your.pem is you security key from Amazon.  When launching a new instance you may need to wait 5 minutes or so for Tomcat to fully start.


For example, if your server's DNS name is: "ec2-50-16-156-210.compute-1.amazonaws.com", your tools would 
be available at:

+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-elr-1.0.0
+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-syndromic-1.0.0
+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-lab-1.0.0
+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-immunization-1.0.0
+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-eprescribing-xml
+ http://ec2-50-16-156-210.compute-1.amazonaws.com:8080/mu-eprescribing-edi


WAR File Downloads
------------------

The WAR files for HL7v2 tools can be found in subversion at the URL below.

+ http://svn.code.sf.net/p/mu-nist/code/tags/

Firewall Configuration
----------------------

This applies for both AMI and WAR configurations.

Open the following inbound ports.

    TCP Port (Service)	Source
    22 (SSH)            0.0.0.0/0
    8080 (HTTP-Tomcat)  0.0.0.0/0
    9080 (MLLP)         0.0.0.0/0


Source Code
-----------

The source code provided here is for information only purposes.  NIST does not 
provide support for source code or source installations.  These links are to
subversion repositories on SourceForge.

These subcomponents are required to build any of the tools:

+ mu-tool-core - http://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core/
+ mu-tool-core-impl-v2  - http://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core-impl/mu-tool-core-impl-hl7-v2
+ hl7-core - http://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-core/trunk/
+ hl7-testframework - http://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-testframework/trunk/
+ Immunization resource-bundle - http://svn.code.sf.net/p/mu-nist/code/trunk/resource-bundle/immunization/
+ ELR resource-bundle - http://svn.code.sf.net/p/mu-nist/code/trunk/resource-bundle/elr/
+ Syndromic resource-bundle - http://svn.code.sf.net/p/mu-nist/code/trunk/resource-bundle/syndromic/


These are the individual Web-based Tomcat projects.

+ Immunization Tool - http://svn.code.sf.net/p/mu-nist/code/branches/mu-tool-ui/mu-tool-ui-hl7-v2/mu-tool-ui-hl7-v2-immunization/
+ Syndromic Tool -  http://svn.code.sf.net/p/mu-nist/code/branches/mu-tool-ui/mu-tool-ui-hl7-v2/mu-tool-ui-hl7-v2-elr/
+ ELR Tool - http://svn.code.sf.net/p/mu-nist/code/branches/mu-tool-ui/mu-tool-ui-hl7-v2/mu-tool-ui-hl7-v2-elr/

