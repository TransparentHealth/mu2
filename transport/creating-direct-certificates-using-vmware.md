Using the VMWare Image to Create Direct Certificates
====================================================

The goal of this document is to demonstrate how to create direct certificates
using NIST's a "canned" version of certGen. This is provided as a conviencece to
make the process of generateing direct certificates easier.

The tool is packaged as a VMWare image and can be ran using VMWarePlayer and
other VMWareTools.

These instructions are based on the instrustions found at
[http://api.nhindirect.org/java/site/assembly/stock/1.3.1/users-guide/depl-hisp-only.html
(http://api.nhindirect.org/java/site/assembly/stock/1.3.1/users-guide/depl-hisp-only.html)

[http://certgen.s3.amazonaws.com/certGen-Ubuntu-12-LTS-64bit.zip]
(VMWare Image that can be downloaded here). The basic steps are as follows:

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
7. Press Ctrl-Alt-T to open up a terminal window.
8. Start the certGen too by typing the following.
    
    cd direct/tools
    ./certGen.sh
    


Create a Root CA
----------------

Create a Root CA/Trust Anchor with the following information.



    CN:                 [Name for your CA - ex. "Root for Direct.Example.com"]
    Country:            [Your Country]
    State:              [Your State]
    Location:           [Your City]
    Org:                [Your Organization Name]
    Email:              [Email for Root CA - ex. root@direct.example.com]
    Experiation Days:   365
    Key Strength:       1024
    Password:           [Your password]

![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/create-a-root-ca.png
"Create a Root CA")



Create a Domin Bound Certificate
--------------------------------


![Screen shot of certGen used to create a domain-bound certificate]
(http://certgen.s3.amazonaws.com/create-a-domain-bound-cert.png
"Create a Domain-Bound Certificate")



Create an Address-Bound Certificate
-----------------------------------

![Screen shot of certGen used to create a address-bound certificate]
(http://certgen.s3.amazonaws.com/create-an-address-bound-cert.png
"Create an Address-Bound Certificate")


These certificates can be used by the Transport Testing Tool, Direct RI, or
other Direct-based system.

