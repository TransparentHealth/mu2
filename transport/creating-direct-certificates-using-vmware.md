Using the VMWare Image to Create Direct Certificates
====================================================

Last updated: February 14, 2013 by Alan Viars

The goal of this document is to demonstrate how to create direct certificates
using NIST's a "canned" version of certGen. This is provided as a convenience to
make the process of generating direct certificates easier.

The tool is packaged as a VMWare image and can be ran using VMWarePlayer and
other VMWare tools.

These instructions are based on the instructions found at
http://api.nhindirect.org/java/site/assembly/stock/1.3.1/users-guide/depl-hisp-only.html

The basic steps are as follows:

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

```
    cd direct/tools
    ./certGen.sh
```

You are now ready to create certificates described in the following sections.


Create a Root CA
----------------

1. Enter the appropriate values for the CA you are creating in the certGen tool
following the example shown in the figure below. You only need to create one CA.

```
    CN:                             [Name for your CA - ex. "Root for Direct.Example.com"]
    Country:                        [Your Country] # Use two letter ISO code, e.g. US.
    State:                          [Your State]
    Location:                       [Your City]
    Org:                            [Your Organization Name]
    Email:                          [Email for Root CA - ex. root@direct.example.com]
    Expiration Days:                365
    Key Strength:                   1024
    Password:                       [Your password]
    Add Email to Alt Subject Names: Checked
```

![Screen shot of certGen used to create a root certificate authority.]
(http://certgen.s3.amazonaws.com/root-CA-for-Direct.png
"Create a Root CA")


2. Click the Create button to create the CA. This will create the files
"root.der" and "rootKey.der" in the /home/ubuntu/direct/tools directory.
If you need to create more leaf certificates from this CA later, You will need
these two files along with your password to reload the CA.



Create a Domain-Bound Certificate
--------------------------------

1. After the CA is created (or loaded), click "Create Leaf Cert" button. Enter
the required values. Be sure to click the “Add Email to Alt Subject Names”.
DO NOT add a password. 

```
    CN:                                 [direct.example.com]
    Country:                            [Your Country] # Use two letter ISO code, e.g. US.
    State:                              [Your State]
    Location:                           [Your City]
    Org:                                [Your Organization Name]
    Email:                              [direct.example.com] #Notice this is not actually an email.
    Expiration Days:                    365
    Key Strength:                       1024
    Password:                           LEAVE BLANK
    Add Email to Alt Subject Names:     Checked
```


![Screen shot of certGen used to create a domain-bound certificate]
(http://certgen.s3.amazonaws.com/domain-bound-direct.png
"Create a Domain-Bound Certificate")


2. Click the "Create" button.This will create the files "direct.example.com.der",
"direct.example.com.p12", and "direct.example.comKey.der" in the
/home/ubuntu/direct/tools/ directory.


Create an Address-Bound Certificate
-----------------------------------


1. After the CA is created (or loaded), click "Create Leaf Cert" button. Enter
the required values. Be sure to click the “Add Email to Alt Subject Names”.
DO NOT add a password. 

```
    CN:                                 [The cert's email e.g. provider1@direct.example.com]
    Country:                            [Your Country] # Use two letter ISO code, e.g. US.
    State:                              [Your State]
    Location:                           [Your City]
    Org:                                [Your Organization Name]
    Email:                              [The cert's email e.g. provider1@direct.example.com]
    Expiration Days:                    365
    Key Strength:                       1024
    Password:                           LEAVE BLANK
    Add Email to Alt Subject Names:     Checked
```


![Screen shot of certGen used to create an address-bound certificate]
(http://certgen.s3.amazonaws.com/address-bound-direct.png
"Create a Address-Bound Certificate")


2. Click the "Create" button.This will create the files "provider1.der",
"provider1.p12", and "provider1Key.der" in the /home/ubuntu/direct/tools/
directory.



![Screen shot of certGen used to create a address-bound certificate]
(http://certgen.s3.amazonaws.com/address-bound-direct.png
"Create an Address-Bound Certificate")


Next Steps
----------

These certificates can be used by the  Direct RI, or other Direct-based system.

