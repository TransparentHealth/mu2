Last Updated March 14, 2014

Transport
=========

These tools cover Direct (including Certificate discovery), CCDA validation,
and SOAP/SAML.

Test Procedures and additional information can be found on HealthIT.gov at
http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures


Transport Testing Tool (TTT)
============================

The Transport Testing Tool provides a variety of functionality and will be used
in several MU2 criteria tests.  TTT performs CCDA validation
(via the MDHT Java library), Direct Testing, and SOAP/SAML.

+ Test Data  - https://github.com/meaningfuluse/mu2/tree/master/transport/test-data
+ NIST's Public Transport Testing Tool Server - http://transport-testing.nist.gov  
+ Transport Testing Tool Configuration - https://github.com/meaningfuluse/mu2/blob/master/transport/ttt-configuration.md
+ TTT Source Code* -http://iheos.svn.sourceforge.net/viewvc/iheos/
+ TTT Source Build Instrutions* - http://ihexds.nist.gov/XdsDocs/Documentation/toolkit-developer-guide.html

*NOTE: NIST does not provide support for source or .war based installations of TTT.

Direct Certificate Discovery Tool
==================================

This tool was developed for ONC by Nitor.  It is a seperate tool than the TTT,
but it is concievable that at some point these two tools' functionality will merge
or more tightly integrate.

+ Public Version hosted by Nitor -  http://www.testteam.us/ModularSpecPhase3_Tool/
+ Source Code and Resources - http://code.google.com/p/direct-certificate-discovery-tool/


MDHT
====

MDHT is used for CCDA Validation and created by Open Health Tools. Since this
project is a Java library, it does not have a free-standing application with a
UI.  This library is integrated into the Transport Testing Tool.

+ MDHT Runtime for NIST - https://github.com/meaningfuluse/mu2/tree/master/transport/mdht
+ MDHT Homepage - https://www.projects.openhealthtools.org/sf/projects/mdht/
+ MDHT Documentation - https://www.projects.openhealthtools.org/sf/projects/mdht/
+ MDHT Source Code - https://www.projects.openhealthtools.org/sf/scm/do/listRepositories/projects.mdht/scm


Useful Links
============

+ [Direct Hello World](https://github.com/meaningfuluse/mu2/blob/master/transport/direct-hello-world.md)
+ [How to Create Direct Certificates using NIST's pre-configured Virtual Machine](https://github.com/meaningfuluse/mu2/blob/master/transport/creating-direct-certificates-using-vmware.md)
