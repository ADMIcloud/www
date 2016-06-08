Local Mode
=============
In order to setup Hadoop properly in your local machine you need to first satisfy the following prerequisites in your environment.

1. Java

   Download Oracle JDK 8 from http://www.oracle.com/technetwork/java/javase/downloads/index.html
   Extract the archive, use the following steps to perform this

.. code-block:: bash

    cd
    mkdir software
    cd software
    wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u91-b14/jdk-8u91-linux-x64.tar.gz"
    tar xzf jdk-8u91-linux-x64.tar.gz


Set the following environment variables. (You can set the variables in the .bashrc file). You can use the following command to open and edit the .bashrc file. We also need to install vim editor to edit the files


.. code-block:: bash

    sudo apt-get install vim
    vim ~/.bashrc

Add the the following lines to the end of the

.. code-block:: bash

    JAVA_HOME=~/software/jdk1.8.0_91
    PATH=$JAVA_HOME/bin:$PATH
    export JAVA_HOME PATH

And run the following command in order to make sure the changes are applied. You should see a output simillar to the one given below after running the code

.. code-block:: bash

    source ~/.bashrc
    java -version

.. code-block:: bash

    java version "1.8.0_66"
    Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
    Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)



2.  SSH and Rsync

    Install SSH and Rsync is not already installed in the environment.

.. code-block:: bash

    sudo apt-get install ssh
    sudo apt-get install rsync

3. Download and extract latest Hadoop binary into your machine. The latest hadoop binary files are available at http://hadoop.apache.org/releases.html. The following commands will download and extract Hadoop version 2.7.2.

.. code-block:: bash

    cd ~/software
    wget http://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
    tar -xzvf hadoop-2.7.2.tar.gz


4. Make sure everything was done properly. Execute the following command from the hadoop folder that we just extracted

.. code-block:: bash

    ./bin/hadoop