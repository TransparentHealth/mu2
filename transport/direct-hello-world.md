Direct Hello World
==================


The goal of this document is to outline the steps to necessary to setting up an instance of Direct. This includes setting up two direct servers, DNS, and certificate configuration.

The end goal is to illustrate how to setup two nhin-d instances and make them exchange information in both directions. This document is a work in Progress.

These instructions assume of Ubuntu 12.04 LTS Server Edition, Java 6, and Direct 1.4.


Firewall Settings:
------------------

Ensure ports 22, 25, 80, 443, 8080, 8081, are open for inbound traffic.



Get Java 6
----------
Download the Linux .bin install (64bit) from Oracle’s site Java Archive.

[http://www.oracle.com/technetwork/java/archive-139210.html](http://www.oracle.com/technetwork/java/archive-139210.html)

Change permissions to allow execute

    chmod a+x jdk-6u34-linux-x64.bin

Run the installer file

    ./jdk-6u34-linux-x64.bin

Setup JAVA_HOME

    export JAVA_HOME=/home/ubuntu/jdk1.6.0_34
    echo "export JAVA_HOME=$JAVA_HOME" >> ~/.bashrc
    echo "export PATH=$PATH:$JAVA_HOME/bin" >> ~/.bashrc
    source ~/.bashrc 

Verify Java 6 is installed

    java -version


Install Cryptographic Extensions
--------------------------------
You must fetch this from Oracle website.  then you can follow the instructions 
below.

    sudo apt-get install unzip
    unzip jce_policy-6.zip
    cp jce/US_export_policy.jar $JAVA_HOME/jre/lib/security
    cp jce/local_policy.jar $JAVA_HOME/jre/lib/security


Get Direct version 1.4
-----------------------
    
    wget http://repo2.maven.org/maven2/org/nhind/direct-project-stock/1.4/direct-project-stock-1.4.tar.gz

Unpack the Direct Project
-------------------------
    
    tar -zxvf direct-project-stock-1.4.tar.gz

Setup More Environment Variables
--------------------------------

    export DIRECT_HOME=`pwd`/direct
    echo "export DIRECT_HOME=$DIRECT_HOME" >> ~/.bashrc
    source ~/.bashrc

Start Tomcat
------------
    
    cd $DIRECT_HOME/apache-tomcat-7.0.27/bin
    ./startup.sh

This process may take a few minutes to complete.  Verify it is working by pointing your browser to: 


    http://[servername]:8081/config-ui/

