Set Up Hadoop
===============


Hadoop
--------

.. figure:: http://hadoop.apache.org/images/hadoop-logo.jpg
   :alt: Hadoop
   :width: 400

Hadoop is an open source framework for distributed storage and processing of large datasets on a commodity cluster. Hadoop utilizes the Hadoop Distributed File System (HDFS) for data storage and the MapReduce model for computational processing. Hadoop 2.0 also includes the YARN resource management platform which manages multiple nodes within the cluster and is responsible for task scheduling.

This section describes how to set up Hadoop on one instance.

Preparation
------------------
1. Java

   Download Oracle JDK 8 from http://www.oracle.com/technetwork/java/javase/downloads/index.html
   Extract the archive using the following steps:

.. code-block:: bash

    cd
    mkdir software
    cd software
    wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u91-b14/jdk-8u91-linux-x64.tar.gz"
    tar xzf jdk-8u91-linux-x64.tar.gz


Set the following environment variables (you can set the variables in the .bashrc file). You can use the following command to open and edit the .bashrc file. We also need to install vim editor to edit the files.


.. code-block:: bash

    sudo apt-get install vim
    vim ~/.bashrc

Add the the following lines to the end of the code:

.. code-block:: bash

    JAVA_HOME=~/software/jdk1.8.0_91
    PATH=$JAVA_HOME/bin:$PATH
    export JAVA_HOME PATH

Now run the following command in order to make sure the changes are applied. You should see an output similar to the one given below after running the code.

.. code-block:: bash

    source ~/.bashrc
    java -version

.. code-block:: bash

    java version "1.8.0_66"
    Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
    Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)

2.  SSH and Rsync

    Install SSH and Rsync are not already installed in the environment. Use these commands to add them.

.. code-block:: bash

    sudo apt-get install ssh
    sudo apt-get install rsync

3. Download and extract the latest Hadoop binary into your machine. These are available at http://hadoop.apache.org/releases.html. The following commands will download and extract Hadoop version 2.7.2.

.. code-block:: bash

    cd ~/software
    wget http://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
    tar -xzvf hadoop-2.7.2.tar.gz


4. Make sure everything was done properly, then execute the following command from the Hadoop folder that we just extracted

.. code-block:: bash

    ./bin/hadoop


Set up passphrase-less ssh
---------------------------

First, check your code with the following command:

.. code-block:: bash

    $ ssh localhost

If you cannot ssh to the localhost without a passphrase, use the following commands to set up passphrase-less ssh:

.. code-block:: bash

    $ cd ~/.ssh
    $ ssh-keygen -t rsa
    (hit enter to all the options)
    $ cat id_rsa.pub >> authorized_keys


Configuration
---------------------------

Modify the following files, replacing $HADOOP_HOME with your own Hadoop home path.

In $HADOOP_HOME/etc/hadoop/hadoop-env.sh, replace ${JAVA_HOME} with your own Java home path. If it is ~/software/jdk1.8.0_91, then add the following:

.. code-block:: bash

    # The java implementation to use.
    export JAVA_HOME=~/software/jdk1.8.0_91#The java implementation to use.

$HADOOP_HOME/etc/hadoop/core-site.xml

.. code-block:: xml

    <configuration>
        <property>
            <name>fs.default.name</name>
            <value>hdfs://localhost:9010</value>
         </property>

        <property>
            <name>hadoop.tmp.dir</name>
            <value>$HADOOP_HOME/tmp</value>
            <description>A base for other temporary directories.</description>
        </property>
    </configuration>


$HADOOP_HOME/etc/hadoop/hdfs-site.xml

.. code-block:: xml

    <configuration>
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>
    </configuration>


$HADOOP_HOME/etc/hadoop/mapred-site.xml

.. code-block:: xml

    <configuration>
        <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
        </property>
    </configuration>


$HADOOP_HOME/etc/hadoop/yarn-site.xml

.. code-block:: xml

    <configuration>
        <property>
            <name>yarn.resourcemanager.hostname</name>
            <value>localhost</value>
        </property>

        <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
        </property>
    </configuration>


Start Daemons
---------------------------

1. Format the file system next.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs namenode -format


If you can see information like this, the format process should be successful.

.. code-block:: bash

    xx/xx/xx xx:xx:xx INFO util.ExitUtil: Exiting with status 0
    xx/xx/xx xx:xx:xx INFO namenode.NameNode: SHUTDOWN_MSG:
    /************************************************************
    SHUTDOWN_MSG: Shutting down NameNode at xxx.xxx.xxx.xxx


2. Launch NameNode daemon and DataNode daemon

.. code-block:: bash

    $ $HADOOP_HOME/sbin/start-dfs.sh


The log is in the $HADOOP_LOG_DIR directory (defaults: $HADOOP_HOME/logs).

3. Check if the daemons started successfully.

.. code-block:: bash

    $ jps
    xxxxx NameNode
    xxxxx SecondaryNameNode
    xxxxx DataNode
    xxxxx Jps


4. Browse the web interface for the NameNode. By default this is at http://localhost:50070

5. Start ResourceManager daemon and NodeManager Daemon

.. code-block:: bash

    $ $HADOOP_HOME/sbin/start-yarn.sh


6. Verify the daemons started sucessfully:

.. code-block:: bash

    $ jps
    xxxxx NameNode
    xxxxx SecondaryNameNode
    xxxxx DataNode
    xxxxx NodeManager
    xxxxx Jps
    xxxxx ResourceManager


7. Browse the web interface for the ResourceManager. By default this should be http://localhost:8088

Example
---------------------------

1. Make the Hadoop Distributed File System (HDFS) directories.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -mkdir -p .
    $ $HADOOP_HOME/bin/hdfs dfs -mkdir input


2. Copy the input files into HDFS. In this example, we use files in $HADOOP_HOME/etc/hadoop/ directory as input files.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -put $HADOOP_HOME/etc/hadoop/* input


3. Run the "grep" example provided.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'hadoop'


4. View the output files on HDFS.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -cat output/*


Or copy the output files to the local filesystem.

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -get output output
    $ cat output/*


Stop daemons.
---------------------------
If you are done, you can stop all daemons by using this code:

.. code-block:: bash

    $ $HADOOP_HOME/sbin/stop-dfs.sh
    $ $HADOOP_HOME/sbin/stop-yarn.sh
