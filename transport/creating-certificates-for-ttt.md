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

As you see, there are 5 conditions to satisfy for Trust Verification.

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
    3. Has not been revoked                 N                 Revoked+        Negative  
    4. Binding to the expected entity       N                 See note++      Negative
    5. Has a trusted certificate path    Partial              See note+++     Negative

\+ **Revocation**: *Revocation via CRL or otherwise, is not supported by the certGen.*

++ **For email-bound certificates**: *If the subjectAltName extension is present
and an rfc822Name is included then it contains the e-mail address.
If the Subject Distinguished Name contains an EmailAddress legacy attribute,
then it contains the e-mail address. If both of the previous locations contain
an e-mail address, they must match.

++ **For domain-bound certificates**: *The subjectAltName extension is present, a
dNSName is included, and it matches the Direct Address' Health Internet Domain.
It is not possible for the "certGen" tool to generate a certificate with The
subjectAltName extension is present, a dNSName is included, and it DOES NOT match
the Direct Address' Health Internet Domain.*

+++ **Trusted Certificate Path**: *The certificate chain is verified all the
way up to the Trust Anchor. ( Version 1.1 of the Direct Applicability Statement
for Secure Health Transport Working does not require checking of the chain back
to the Root CA, but rather just to the Trust Anchor. The current test procedure
only tests one hop.  That is to say, it assumes that the endpoint certificate
was created by the trust anchor and there are no intermediate certificates.*


Experimental support for conditions 3 and 4 are possible using https://DirectCA.org,
but currently this is approach is not officially supported.  See section "Other
Negative Tests" for more details.

Certificates to Create
======================

Good Certificates
------------------
   1. a Trust Anchor, we will name "example.com". You will need the file "example.der".
   2. A domain-bound certificates, built from the aforementioned "example.com", for
   "ttt.example.com". You will need the files "ttt.example.com.p12" and
   "ttt.example.com.der".

Negative Certificates
----------------------

   1. An expired certificate.
   2. An invalid trust relationship. We will call this
   "invalid-trust-relationship" in these instructions. This anchor is valid,
   but the children node were not created by this trust anchor.Use this in conjunction with
   "ttt.your-domain.com" from #2.  These node Certificates then it would be
   invalid because the domain-bound certificate was not created with this trust
   anchor.

Other Negative Tests
--------------------

ATLs are currently not testing 3, 4, and are partially testing 5.


3. Revocation -  The Direct Applicbility Statement version 1.1 does not specify
how revocation is to be performed.  The Java RI implements certificate revocation
lists (CRL), but other Direct implementation do it some other way.The web based
certificate generation tool, https://DirectCA.org  , provides experimental
support for this test, but this is not officially supported.

4. Binding to the expected entity - It is not possible for the "certGen" tool
to generate a certificate with The subjectAltName extension is present, a
dNSName is included, and it DOES NOT matches the Direct Address' Health
Internet Domain.  Other tools attempt to prevent this mistake as well.
The web based certificate generation tool, https://DirectCA.org  , provides
experimental support for this test, but this is not officially supported.

5. The test procedure needs to be updated to require the building of a multi-node
chain (2 nodes or more away from the Trust Anchor).


Running the Canned version of CertGen using VMWare
==================================================

1. Download the file [http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip]
(http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip)
2. Unzip the file "certGen-Ubuntu-12-LTS-64bit.zip" to a folder such as "certgen-vm".
3. Download VMWare Player from [http://www.vmware.com/products/player/]
(http://www.vmware.com/products/player/)
4. Start VMWare Player.
5. Press Ctrl-O to open a VMWare Image
6. Navigate to folder extracted in step 1 and select the file
"Direct certGen - Ubuntu64-bit.vmx" and click "Open".
7. When the virtual VM is completely booted you will see a Desktop.
There is no password or login required, but the user and password are:
ubuntu/adm1nD1r3ct
8. Press Ctrl-Alt-T to open up a terminal window.
9. Start the certGen tool.

Type this into the terminal:

```
    cd direct/tools
    ./certGen.sh
```
Details for creating a each type of certificate is detailed  in the following
sections.


Create a Good Root CA / Trust Anchor (# 2)
==========================================

This certificate is for testing "2. Has a valid signature ".  It is used in
conjunction with the domain-bound endpoint certificate decribed in the next
section of this document.

1. Enter the appropriate values for the CA you are creating in the certGen tool
following the example shown in the figure below. You only need to create one CA.

```
    CN:                             [Name for your CA - ex. "example.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [example.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked
```
![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/CA1.png "Create a Root CA")


2. Click the Create button to create the CA. This will create the files
"example.com.der" and "example.comKey.der" in the /home/ubuntu/direct/tools directory.
If you need to create more leaf certificates from this CA later, You will need
these two files along with your password to reload the CA.



Create a Good Domain Bound Certificate (#2)
===========================================

This certificate is for testing "2. Has a valid signature ".  It is used in
conjunction with the trust anchor certificate decribed in the previous
section of this document.


1. After the CA is created (or loaded), click "Create Leaf Cert" button. Enter
the required values. Be sure to click the “Add Email to Alt Subject Names”.
DO NOT add a password. 

```
    CN:                                 [ttt.example.com]
    Country:                            [Your Country] # Use two letter ISO code, e.g. US.
    State:                              [Your State]
    Location:                           [Your City]
    Org:                                [Your Organization Name]
    Email:                              [ttt.example.com] #Notice this is not actually an email.
    Expiration Days:                    365
    Key Strength:                       1024
    Password:                           LEAVE BLANK
    Add Email to Alt Subject Names:     Checked
```


![Screen shot of certGen used to create a domain-bound certificate]
(http://certgen.s3.amazonaws.com/domain-bound-cert.png
"Create a Domain-Bound Certificate")


2. Click the "Create" button.This will create the files "ttt.example.com.der",
and "ttt.example.com.p12" in the /home/ubuntu/direct/tools/ directory.

3. Copy the trust anchor and domain-bound endpoint certificates to files to
their own directory called "good".  This is necessary because the next steps will
overwrite these files because they will have the same file names.
```
    mkdir good
    mv example.com* good
    mv ttt.example.com* good
```


Expired (#2)
============


1. Using the Good Trust Anchor from above, create an expired domain-bound
 endpoint certificate.

```
    CN:                             [Name for your CA - ex. "ttt.example.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [ttt.example.com]
    Expiration Days:                0
    Key Strength:                   1024
    Password:                       LEAVE BLANK
    Add Email to Alt Subject Names: Checked
```
![Screen shot of certGen used to create an expired domain-bound certificate]
(http://certgen.s3.amazonaws.com/expired.png
"Create an Expired Domain-Bound Certificate")


2. Copy this domain-bound endpoint's certificates to their own directory
called "expired".  This is necessary because the next steps will
overwrite these files because they will have the same file names.

```
    mkdir expired
    mv ttt.example* expired
```


Create Another Root CA / Trust Anchor to be Used in the Invalid Trust Relationship Test (#5)
============================================================================================

1. Restart certGen.sh.

2. Enter the appropriate values for the CA you are creating in the certGen tool
following the example shown in the figure below. You only need to create one CA.

```
    CN:                             [Name for your CA - ex. "example.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [example.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked
```
![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/CA2.png
"Create a Root CA")


3. Click the Create button to create the CA. This will create the files
"example.com.der" and "example.comKey.der" in the
/home/ubuntu/direct/tools directory.  Place these in their own directory called,
invalid-trust-relationship" to avoid confusing this with your other sets of certificates.

```
    mkdir invalid-trust-relationship
    cp example.com* invalid-trust-relationship
    cp good/ttt.example.com* invalid-trust-relationship
```
The above step will result in a folder containing a trust anchor and endpoint that
do not match.


4. Install this example.com.der into the Direct implmentation.  Then attempt to
send a Direct message using the certificate from the "Good Domain Bound Certificate"
step.  Since this endpoint certificate was not created by this trust anchor, despite
the fact the DNS, Common Name (CN), and Email, and Subject are the same. 

Please refer to ttt-configuration.md for more explanation on installation of these
certificates.
