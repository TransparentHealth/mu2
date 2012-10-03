NIST Syndromic
==============

+ [Testing Procedure](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)
+ [Temporary Testing Server URL](http://lri.sipilotdevelopment.org/mu-syndromic/)

WAR File
--------

The latest WAR file is here.

+ [mu-immunization.war](http://svn.code.sf.net/p/mu-nist/code/trunk/mu-wars/mu-syndromic.war)


Use this file to configure Tomcat 7.


Firewall Configuration
----------------------

Open the following inbound ports.

    TCP Port (Service)	Source
    22 (SSH)            0.0.0.0/0			
    8080 (HTTP-Tomcat)  0.0.0.0/0
    9080 (MLLP)         0.0.0.0/0


Source Code:
------------

The source code provided here is for information only purposes.  NIST does not 
provide support for source code or source installations.

These subcomponents are required to build an Syndromic Test Tool WAR File:

+ mu-tool-core - [https://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core/](https://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core/)

+ mu-tool-core-impl-v2  [https://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core-impl/mu-tool-core-impl-hl7-v2](https://svn.code.sf.net/p/mu-nist/code/trunk/mu-tool-core-impl/mu-tool-core-impl-hl7-v2/)

+ resource-bundle - [https://svn.code.sf.net/p/mu-nist/code/trunk/resource-bundle/immunization/](https://svn.code.sf.net/p/mu-nist/code/trunk/resource-bundle/immunization/)

+ hl7-core - [https://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-core/trunk/](https://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-core/trunk/)

+ hl7-testframework - [https://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-testframework/trunk/](https://hl7-nist.svn.sourceforge.net/svnroot/hl7-nist/hl7-testframework/trunk/])

+ Syndromic Tool - [http://svn.code.sf.net/p/mu-nist/code/branches/mu-tool-ui/mu-tool-ui-hl7-v2/mu-tool-ui-hl7-v2-syndromic/](http://svn.code.sf.net/p/mu-nist/code/branches/mu-tool-ui/mu-tool-ui-hl7-v2/mu-tool-ui-hl7-v2-syndromic/)

