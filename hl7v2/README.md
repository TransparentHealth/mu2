NIST HL7 v2 Tools
=================

Last Updated: March. 14, 2014 by Alan Viars

These test tools include Electronic Immunization, Lab Results Interface (LRI), Lab Reporting (ELR), and Syndromic.

Below are links to the links, installation information, and other related information.


Immunization Test Tool
----------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL -  (http://hl7v2-iz-testing.nist.gov)
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-immunization-1.8.0.zip

Lab Results Interface (LRI) Test Tool
-------------------------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-lab-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-lab-1.7.0.zip


Electronic Lab Reporting (ELR) Test Tool
-----------------------------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-elr-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-elr-1.8.0.zip

Syndromic Test Tool
-------------------

+ Testing Procedure - http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures
+ NIST Testing Server URL - http://hl7v2-ss-testing.nist.gov
+ War Download - http://svn.code.sf.net/p/mu-nist/code/tags/mu-syndromic-1.7.0.zip


Ubuntu Linux Installation Instructions
--------------------------------------

These instructions assume an Ubuntu 12.04 LTS 64bit Server edition.
These instrustion are meant to be follwed from a command prompt.

Ensure the packahe repository is up to date.

    sudo apt-get update

Install Tomcat7. This will also install OpenJDK 1.6

    sudo apt-get -y install tomcat7 tomcat7-admin unzip

Fetch the zipped archives containing the WAR fiels from subversion/elsewhere

	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-syndromic-1.7.0.zip
	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-lab-1.7.0.zip
	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-eprescribing-1.0.4.zip
	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-elr-1.8.0.zip
	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-actor-1.0.1.zip
	wget http://svn.code.sf.net/p/mu-nist/code/tags/mu-immunization-1.8.0.zip
    wget http://hit-testing.nist.gov/cda-validation/downloads/muCrValidation20130206.tar.gz


Unzip the files and copy the War files the webapps folder for deployment

    unzip mu-elr-1.8.0.zip
    sudo cp mu-elr-1.8.0/mu-elr.war /var/lib/tomcat7/webapps/

    unzip  mu-immunization-1.8.0.zip
    sudo cp mu-immunization-1.8.0/mu-immunization.war /var/lib/tomcat7/webapps/

    unzip mu-actor-1.0.1.zip
    sudo cp mu-actor-1.0.1/mu-actor.war /var/lib/tomcat7/webapps/mu-actor.war

    unzip mu-lab-1.7.0.zip
    sudo cp  mu-lab-1.7.0/mu-lab.war  /var/lib/tomcat7/webapps/

    unzip mu-syndromic-1.7.0.zip
    sudo cp mu-syndromic-1.7.0/mu-syndromic.war /var/lib/tomcat7/webapps/

    unzip mu-eprescribing-1.0.4.zip
    sudo cp mu-eprescribing-edi.war  /var/lib/tomcat7/webapps/
    sudo cp mu-eprescribing-xml.war /var/lib/tomcat7/webapps/

    tar zxvf muCrValidation20130206.tar.gz
    sudo cp muCrValidation20130206/muValidationCR.war /var/lib/tomcat7/webapps/


If your server's IP is "127.0.0.1", then you can access the tools at the following URLS.

+ http://127.0.0.1:8080/mu-elr
+ http://127.0.0.1:8080/mu-syndromic
+ http://127.0.0.1:8080/mu-lab
+ http://127.0.0.1:8080/mu-immunization
+ http://127.0.0.1:8080/mu-eprescribing-xml
+ http://127.0.0.1:8080/mu-eprescribing-edi
+ http://127.0.0.1:8080/muValidationCR

Windows Installation Instructions
---------------------------------

Windows instructions can be found inside the tool itself.  From within the HL7v2 tool.
Hint: See the NIST Testing Server URL's decribed in the first section.

From within the tool, Click on the Documentation tab.  Then navigate to the "Resource.
Documentation-->Validation Tool Download" section.  You will see a link to downloan Windows installation instructions.


VMWare Image
------------

The following VMWare image is based on Ubuntu 12.04 and has Apache Tomcat 7 and
the projects already installed.    You will need to install VMWare, such as VMWare Player which is free, to get these tool up and running.  It is based on the instructions desrcibes in "Ubuntu Linux Installation Instructions" section. OpenSSH-server is also installed so you can access the boc via ssh. We reccomend at least 3GB of RAM for this VM.


*Download:*  http://meaningfuluse.s3.amazonaws.com/NIST-HL7v2-eRx-CancerReg-Sept-30-2013.zip
*Name:*      NIST-HL7v2-eRx-CancerReg-Sept-30-2013
*User:*      ubuntu
*Password:*  homerlovesdonuts


Archived VMWare versions are here.

*Download:*  http://meaningfuluse.s3.amazonaws.com/NIST-HL7v2-eRx-CancerReg-Feb-18-2013.zip
*Name:*      NIST-HL7v2-eRx-CancerReg-Feb-18-2013




Amazon Machine Images (AMI)
---------------------------

The AMI is based on Ubuntu 12.04 64-bit and has Apache Tomcat 7 and the
projects are already installed. It is based on the instructions described in
"Ubuntu Linux Installation Instructions" section.



*AMI ID:* ami-15095e7c.

*AMI Name:* NIST-HL7v2-eRx-CancerReg-sept-30-2013

*Description:* NIST HL7v2, ePrescribing, and Cancer Registry Test Tools versions
packaged on September 30 2013. Runs atop Ubuntu 12.04 LTS, Tomcat7, OpenJDK 1.6.  Like all ubuntu AMIs, use the username "ubuntu" instead of "root" when accessing via ssh.

Notes:  Use at least a "medium" instance size. Be sure to follow the firewall
instructions in this document for your instance. When connecting to the host
via ssh, remember that the default user name is "ubuntu" and  not "root".  Be
sure and "chmod 400 your.pem", where your.pem is you security key from Amazon.
When launching a new instance you may need to wait 5 minutes or so for Tomcat7
to fully start.

Archived AMIs are here:

*AMI ID:* ami-1a1e8e73
*AMI Name:* NIST-HL7v2-eRx-CancerReg-Feb-18-2013
*Description:* NIST HL7v2, ePrescribing, and Cancer Registry Test Tools versions
released Feb. 15 2013. Runs atop Ubuntu 12.04 LTS, Tomcat7, OpenJDK 1.6.




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

