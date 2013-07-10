Using the VMWare Image to Create Certificates for Transport Testing Tool (TTT)
==============================================================================

Last updated: July 10th, 2013 by Alan Viars

The goal of this document is to demonstrate how to create direct certificates
for use with NIST's Transport Testing Tool (TTT). If you have a "headless" server
you will need to use X11 or another tool build the certificates.  You can also
use NIST's "canned" version of certGen as described here. This is provided as a
convenience to make the process of running certGen easier. This is simply the
certGen tool packaged as a VMWare image (Ubuntu) and can be ran using
the free VMWarePlayer or other commercial VMWare.

However you choose to get certGen up and running, you can use these instructions
as a guide to creating the certificates necessary to configure TTT.

Overview of Certificate Creation
================================

As a primer, please read section 4 "Trust Verification" in

http://wiki.directproject.org/file/view/Applicability%20Statement%20for%20Secure%20Health%20Transport%20v1.1.pdf

As you see there are 5 criteria for Trust Verification.

1. Has not expired
2. Has a valid signature
3. Has not been revoked
4. Binding to the expected entity
5. Has a trusted certificate path

Not all of these tests are currently supported by NIST.  The following table
outlines current support.

    Conditions                          Supported      Certificate to Create  Test Type  
    ==========                          =========      =====================  =========
    1. Has not expired                      Y                 Expired         Negative
    2. Has a valid signature                Y                 Good            Positive  
    3. Has not been revoked                 N                 Revoked*        Negative  
    4. Binding to the expected entity       N                 See note**       Negative
    5. Has a trusted certificate path    Partial              See note***     Negative

* Revocation, via CRL or otherwise, is not supported by the certGen.

** *For email-bound certificates*: If the subjectAltName extension is present
and an rfc822Name is included then it contains the e-mail address.
If the Subject Distinguished Name contains an EmailAddress legacy attribute,
then it contains the e-mail address.
If both of the previous locations contain an e-mail address, they must match.
*For domain-bound certificates*: The subjectAltName extension is present, a
dNSName is included, and it matches the Direct Address' Health Internet Domain.

** It is not possible for the "certGen" tool to generate a certificate with The
subjectAltName extension is present, a dNSName is included, and it DOES NOT matches
the Direct Address' Health Internet Domain.

*** The certificate chain is verified all the way up to the Trust Anchor. ( Version 1.1
of the Direct Applicability Statement for Secure Health Transport Working does
not require checking of the chain back to the Root CA, but rather just to the Trust Anchor.
The current test procedure only tests one hop.  That is to say, it assumes that the
endpoint certificate was created by the trust anchor and there are no intermediate
certificates.


Experimental support for conditions 3 and 4 are possible using https://DirectCA.org,
but currently this is approach is not officially supported.  See section "Other
Negative Tests" for more details.

You will need to create:

*Good Certificates-*

   1. a Trust Anchor, we will name "your-domain.com". You will need the file "your-domain.der".
   2. A domain-bound certificates, built from the aforementioned "your-domain.", for
   "ttt.your-domain.com". You will need the files "ttt.your-domain.com.p12" and
   "ttt.your-domain.com.der".

*Negative Certificates-*

   1. An expired certificate.
   2. An invalid trust relationship. We will call this
   "invalid-trust-relationship" in these instructions. This anchor is valid,
   but the children node were not created by this trust anchor.Use this in conjunction with
   "ttt.your-domain.com" from #2.  These node Certificates then it would be
   invalid because the domain-bound certificate was not created with this trust
   anchor.

*Other Negative Tests*

ATLs are currently not testing 3, 4, and are partially testing 5.


# 3. Revocation -  The Direct Applicbility Statement version 1.1 does not specify
how revocation is to be performed.  The Java RI implements certificate revocation
lists (CRL), but other Direct implementation do it some other way.The web based
certificate generation tool, https://DirectCA.org  , provides experimental
support for this test, but this is not officially supported.

# 4. Binding to the expected entity - It is not possible for the "certGen" tool
to generate a certificate with The subjectAltName extension is present, a
dNSName is included, and it DOES NOT matches the Direct Address' Health
Internet Domain.  Other tools attempt to prevent this mistake as well.
The web based certificate generation tool, https://DirectCA.org  , provides
experimental support for this test, but this is not officially supported.

# 5. The test procedure needs to be updated to require the building of a multi-node
chain (2 nodes or more from the Trust anchor).


Running the Canned version of CertGen using VMWare
--------------------------------------------------

1. Download the file [http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip]
(http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip)
1. Unzip the file "certGen-Ubuntu-12-LTS-64bit.zip" to a folder such as "certgen-vm".
2. Download VMWare Player from [http://www.vmware.com/products/player/]
(http://www.vmware.com/products/player/)
3. Start VMWare Player.
4. Press Ctrl-O to open a VMWare Image
5. Navigate to folder extracted in step 1 and select the file
"Direct certGen - Ubuntu64-bit.vmx" and click "Open".
6. When the virtual VM is completely booted you will see a Desktop.
There is no password or login required, but the user and password are:
ubuntu/adm1nD1r3ct
7. Press Ctrl-Alt-T to open up a terminal window.
8. Start the certGen tool.

Type this into the terminal:

    cd direct/tools
    ./certGen.sh

Details for creating a each type of certificate is detailed  in the following
sections.


Create a Root CA / Trust Anchor
-------------------------------

1. Enter the appropriate values for the CA you are creating in the certGen tool
following the example shown in the figure below. You only need to create one CA.


    CN:                             [Name for your CA - ex. "Root for your-domain.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [Email for Root CA - ex. root@ttt.your-domain.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked

![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/CA1.png "Create a Root CA")


2. Click the Create button to create the CA. This will create the files
"root.der" and "rootKey.der" in the /home/ubuntu/direct/tools directory.
If you need to create more leaf certificates from this CA later, You will need
these two files along with your password to reload the CA.



Create a Domain Bound Certificate
---------------------------------

1. After the CA is created (or loaded), click "Create Leaf Cert" button. Enter
the required values. Be sure to click the “Add Email to Alt Subject Names”.
DO NOT add a password. 

    CN:                                 [ttt.your-domain.com]
    Country:                            [Your Country] # Use two letter ISO code, e.g. US.
    State:                              [Your State]
    Location:                           [Your City]
    Org:                                [Your Organization Name]
    Email:                              [ttt.your-domain.com] #Notice this is not actually an email.
    Expiration Days:                    365
    Key Strength:                       1024
    Password:                           LEAVE BLANK
    Add Email to Alt Subject Names:     Checked



![Screen shot of certGen used to create a domain-bound certificate]
(http://certgen.s3.amazonaws.com/domain-bound-cert.png
"Create a Domain-Bound Certificate")


2. Click the "Create" button.This will create the files "direct.example.com.der",
"direct.example.com.p12", and "direct.example.comKey.der" in the
/home/ubuntu/direct/tools/ directory.


Negative 1: Expired
--------------------


 1. An expired certificate.

    CN:                             [Name for your CA - ex. "Invalid trust relationship for your-domain.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [ttt.your-domain.com]
    Expiration Days:                0
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked

![Screen shot of certGen used to create an expired domain-bound certificate]
(http://certgen.s3.amazonaws.com/expired.png
"Create an Expired Domain-Bound Certificate")


Negative 2: Invalid
-------------------

This is invalid certificate whereby the email subject name is bogus.

    CN:                             [Name for your CA - ex. "Invalid trust relationship for your-domain.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [bogus.domain.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked


![Screen shot of certGen used to create an invalid domain-bound certificate]
(http://certgen.s3.amazonaws.com/invalid.png
"Create an Invalid  Domain-Bound Certificate")


Negative 3: Create Another Root CA / Trust Anchor to be Used in the Invalid Trust Relationship Test
---------------------------------------------------------------------------------------------------

1. Close the leaf node dialog if certGen.sh, and the restart it.

2. Enter the appropriate values for the CA you are creating in the certGen tool
following the example shown in the figure below. You only need to create one CA.


    CN:                             [Name for your CA - ex. "Invalid trust relationship for your-domain.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [root@otheranchor.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked

![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/CA2.png
"Create a Root CA")


3. Click the Create button to create the CA. This will create the files
"root.der" and "rootKey.der" in the /home/ubuntu/direct/tools directory.
If you need to create more leaf certificates from this CA later, You will need
these two files along with your password to reload the CA.


