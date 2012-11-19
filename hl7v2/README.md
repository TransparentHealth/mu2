NIST HL7 v2 Tools
=================

These tools include Electronic Lab Reporting (ELR), Immunization,
Lab Results Interface (LRI), and Syndromic.


Immunization Test Tool
----------------------

+ [Testing Procedure](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)
+ [Temporary Testing Server URL](http://lri.sipilotdevelopment.org/mu-immunization/)


Lab Results Interface (LRI) Test Tool
-------------------------------------

+ [Testing Procedure](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)
+ [Temporary Testing Server URL](http://lri.sipilotdevelopment.org/lri-dstu)


Electronic Lab Reporting (ELR) Test Tool
-----------------------------------------

+ [Testing Procedure](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)
+ [Temporary Testing Server URL](http://lri.sipilotdevelopment.org/mu-elr/)

Syndromic Test Tool
-------------------

+ [Testing Procedure](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)
+ [Temporary Testing Server URL](http://lri.sipilotdevelopment.org/mu-syndromic/)



Amazon Machine Images
---------------------

These AMI's  are based on Ubuntu 12.04 64-bit and has Apache Tomcat 7 and the 
projects already installed. 


AMI:  ami-d5f848bc
Description: NIST Immunization Tool 1.0.0 September 12, 2012

TODO: Add all projects into a single AMI for download.

VMWare Images
-------------


TODO: Add all projects into a single AMI for download.


WAR File Downloads
------------------

Use these file to configure Tomcat 7 if you are not using the AMI.

+ [mu-immunization.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/mu-immunization.war)
+ [lri-dstu.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/lri-dstu.war)
+ [mu-elr.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/mu-elr.war)
+ [mu-immunization.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/mu-immunization.war)
+ [mu-syndromic.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/mu-syndromic.war)
+ [test-story-manager.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/test-story-manager.war)


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

These subcomponents are required to build an any of the tools:

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

