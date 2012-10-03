Transport Testing Tool (TTT)
============================


The Trasnport Testing Tool provides a variety of functionality and will be used
in several MU2 criteria tests.  TTT performs CCDA validation, Direct Testing,
and SOAP/SAML.


The Test Procedure and attional information can be found on HealthIT.gov at
[http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures](http://www.healthit.gov/policy-researchers-implementers/2014-edition-draft-test-procedures)

The following sections point to information on pointers to servers running 
the tool and local configuration of the tool.


TTT
---

+ [Transport Testing Tool](http://hit-testing.nist.gov:9100/ttt/)
+ [Test Data](/meaningfuluse/mu2/tree/master/transport/test-data)



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

In this example, we will use the IP "54.243.204.42" as the hostnme and "transparenthealth.org"

Setup the A, CNAME as show below.
 
    Type:           Source:                         Destination:	
    A Record        *.transparenthealth.org         54.243.204.42	
    A Record	    transparenthealth.org           54.243.204.42	
    CNAME Record    mail.transparenthealth.org      transparenthealth.org	
    CNAME Record    smtp.transparenthealth.org      transparenthealth.org	


Setup the MX (Mail) Records as shown below.

    mail.transparenthealth.org	20
    smtp.transparenthealth.org	10



Certificate Creation and Installation
-------------------------------------


(This section needs a volunteer.)


Developer Docuemntation
-----------------------

+ **Transport Testing Tool** - The subversion source code repository is hosted
on SourceForge and can [be found here](http://iheos.svn.sourceforge.net/viewvc/iheos/)
and [build instructions can be found here](http://ihexds.nist.gov/XdsDocs/Documentation/toolkit-developer-guide.html).
A NIST hosted version of this software is [http://nist-ttt.org:8080/ttt](http://nist-ttt.org:8080/ttt).

 **NIST does not support source-based installations. Instead you should use the
 binary WAR or AMI distribution.**  These will placed on this page when available. 


+ **MDHT** - MDHT is used for CCDA Validation.  The project's [homepage is here]
(https://www.projects.openhealthtools.org/sf/projects/mdht/). The subversion
source repository, hosted by Open Health Tools,
[is found here](https://www.projects.openhealthtools.org/sf/scm/do/listRepositories/projects.mdht/scm).
Documentation for MDHT can be [found here](https://www.projects.openhealthtools.org/sf/projects/mdht/).
Since this project is a library, it does not have a free-standing application
with a UI.  It's capabilites are be part of the Transport Testing Tool.

The [NIST MDHT tool for MU2 certification can may be found here](/meaningfuluse/mu2/tree/master/transport/mdht)




