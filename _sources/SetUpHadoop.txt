Set up Hadoop
=============

.. figure:: http://hadoop.apache.org/images/hadoop-logo.jpg
   :alt: Hadoop
   :width: 400

Hadoop is an open source framework for distributed storage and processing of large datasets on a commodity cluster. Hadoop utilizes the Hadoop Distributed File System (HDFS) for data storage and the MapReduce model for computational processing. Hadoop 2.0 also includes the YARN resource management platform which manages multiple nodes within the cluster and is responsible for task scheduling.

This section describes how to set up Hadoop on one instance.

Preparation
-----------

1. Create Directories

    We'll assume the following directory structure, where **examples** contains the code examples for this workshop.
    The **software** directory holds unzipped software, while **zips** contains the software tarballs.

    .. code-block:: bash

        /home/ubuntu
        ├── examples
        ├── software
        └── zips

    If you don't see this structure, you can create it using the following command.

    .. code-block:: bash

        mkdir ~/{examples,software,zips}

2. Vim

    Install vim, the command-line text editor using the following command.

    .. code-block:: bash

        sudo apt-get install vim

    .. note:: Once inside vim, to edit text you'll first need to press the letter **i**.
        Then to escape from the editing mode, you can press **Esc** key. To save, press **:w** and
        to quit, press **:q**

3. SSH and Rsync

    Install SSH and Rsync are not already installed in the environment. Use the following commands to install them.

    .. code-block:: bash

        sudo apt-get install ssh
        sudo apt-get install rsync

4. Java

    Download Oracle JDK 8 and extract the archive using the following steps.

    .. code-block:: bash

        cd ~/software
        wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u91-b14/jdk-8u91-linux-x64.tar.gz"
        tar xzf jdk-8u91-linux-x64.tar.gz
        mv jdk-8u91-linux-x64.tar.gz ~/zips

5. Apache Maven

    Download Apache Maven 3 and unzip it using the following commands.

    .. code-block:: bash

        cd ~/software
        wget http://ftp.wayne.edu/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
        tar xzf apache-maven-3.3.9-bin.tar.gz
        mv apache-maven-3.3.9-bin.tar.gz ~/zips

6. Apache Hadoop

    Download and extract the latest Hadoop binary into your machine.
    These are available at http://hadoop.apache.org/releases.html.
    The following commands will download and extract Hadoop version 2.7.2.

    .. code-block:: bash

        cd ~/software
        wget http://www-eu.apache.org/dist/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz
        tar -xzvf hadoop-2.7.2.tar.gz
        mv hadoop-2.7.2.tar.gz ~/zips


6. Environment Variables

    Set the following environment variables (you can set the variables at the **top** of the ~/.bashrc file).
    You can use the following command to open and edit the ~/.bashrc file.

    .. code-block:: bash

        vim ~/.bashrc

    Add the the following lines to the beginning of the file.

    .. code-block:: bash

        MVN_HOME=~/software/apache-maven-3.3.9
        JAVA_HOME=~/software/jdk1.8.0_91
        PATH=$MVN_HOME/bin:$JAVA_HOME/bin:$PATH
        export JAVA_HOME PATH

        HADOOP_HOME=~/software/hadoop-2.7.2
        export HADOOP_YARN_HOME=$HADOOP_HOME
        export HADOOP_PREFIX=$HADOOP_HOME
        export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

    Now run the following command in order to make sure the changes are applied.

    .. code-block:: bash

        source ~/.bashrc
        java -version

    You should see an output similar to the one given below.

    .. code-block:: bash

        java version "1.8.0_91"
        Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
        Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

6. Verify Apache Hadoop

    Check if you can successfully run the following Apache Hadoop command.

    .. code-block:: bash

        cd $HADOOP_PREFIX
        ./bin/hadoop

    You should see the following output.

    .. code-block:: bash

        Usage: hadoop [--config confdir] [COMMAND | CLASSNAME]
          CLASSNAME            run the class named CLASSNAME
         or
          where COMMAND is one of:
          fs                   run a generic filesystem user client
          version              print the version
          jar <jar>            run a jar file
                               note: please use "yarn jar" to launch
                                     YARN applications, not this command.
          checknative [-a|-h]  check native hadoop and compression libraries availability
          distcp <srcurl> <desturl> copy file or directories recursively
          archive -archiveName NAME -p <parent path> <src>* <dest> create a hadoop archive
          classpath            prints the class path needed to get the
          credential           interact with credential providers
                               Hadoop jar and the required libraries
          daemonlog            get/set the log level for each daemon
          trace                view and modify Hadoop tracing settings

        Most commands print help when invoked w/o parameters.

Set up Password-less SSH
------------------------

Test if you can SSH to **localhost** without requiring a password.

.. code-block:: bash

    ssh localhost

If the above requires a password then setup password-less SSH using the following commands.

.. code-block:: bash

    cd ~/.ssh
    ssh-keygen -t rsa
    (hit enter to all the options)
    cat id_rsa.pub >> authorized_keys

Then try to SSH again and if that's successful hit exit to terminate that SSH connection

.. code-block:: bash

    ssh localhost
    exit

Apache Hadoop Configuration
---------------------------

Modify the following files in Apache Hadoop distribution.

.. note:: Make sure the environment variable $HADOOP_PREFIX is set correctly before proceeding with the following.

1. core-site.xml

    .. code-block:: bash

        vim $HADOOP_PREFIX/etc/hadoop/core-site.xml

    .. code-block:: xml

        <configuration>
            <property>
                <name>fs.default.name</name>
                <value>hdfs://localhost:9010</value>
             </property>

            <property>
                <name>hadoop.tmp.dir</name>
                <value>/tmp/hadoop-${user.name}</value>
                <description>A base for other temporary directories.</description>
            </property>
        </configuration>

2. hdfs-site.xml

    .. code-block:: bash

        vim $HADOOP_PREFIX/etc/hadoop/hdfs-site.xml

    .. code-block:: xml

            <configuration>
                <property>
                    <name>dfs.hosts</name>
                    <value>/home/ubuntu/software/hadoop-2.7.2/etc/hadoop/slaves</value>
                </property>

                <property>
                    <name>dfs.replication</name>
                    <value>1</value>
                </property>
                <property>
                    <name>dfs.namenode.http-address</name>
                    <value>localhost:50070</value>
                </property>
                <property>
                    <name>dfs.namenode.secondary.http-address</name>
                    <value>localhost:50190</value>
                </property>
            </configuration>

3. mapred-site.xml

    .. code-block:: bash

        vim $HADOOP_PREFIX/etc/hadoop/mapred-site.xml

    .. code-block:: xml

        <configuration>
            <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
            </property>
        </configuration>

4. yarn-site.xml

    .. code-block:: bash
        vim $HADOOP_PREFIX/etc/hadoop/yarn-site.xml

    .. code-block:: xml

            <configuration>
            <property>
                <name>yarn.resourcemanager.address</name>
                <value>localhost:8132</value>
            </property>
            <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>localhost:8130</value>
            </property>
            <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>localhost:8131</value>
            </property>
            <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>localhost:8133</value>
            </property>
            <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>localhost:8080</value>
            </property>
            <property>
                <name>yarn.scheduler.maximum-allocation-mb</name>
                <value>4096</value>
            </property>
            <property>
                <name>yarn.nodemanager.resource.memory-mb</name>
                <value>4096</value>
            </property>

            <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
            </property>
        </configuration>


Start Daemons
-------------

1. Format the file system next.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hdfs namenode -format

    If you can see information like below, the format process should be successful.

    .. code-block:: bash

        xx/xx/xx xx:xx:xx INFO util.ExitUtil: Exiting with status 0
        xx/xx/xx xx:xx:xx INFO namenode.NameNode: SHUTDOWN_MSG:
        /************************************************************
        SHUTDOWN_MSG: Shutting down NameNode at xxx.xxx.xxx.xxx


2. Launch NameNode daemon and DataNode daemon

    .. code-block:: bash

        $HADOOP_PREFIX/sbin/start-dfs.sh


    The log is in the $HADOOP_LOG_DIR directory (default is $HADOOP_PREFIX/logs).

3. Check if the daemons started successfully.

    .. code-block:: bash

        jps

    You should see the following with xxxxx replaced to actual process IDs.

    .. code-block:: bash

        xxxxx NameNode
        xxxxx SecondaryNameNode
        xxxxx DataNode
        xxxxx Jps

4. Browse the web interface for the NameNode. By default this is at http://localhost:50070

5. Start ResourceManager daemon and NodeManager Daemon

    .. code-block:: bash

        $HADOOP_PREFIX/sbin/start-yarn.sh


6. Verify the daemons started sucessfully:

    .. code-block:: bash

        jps

    You should see the following with xxxxx replaced by actual process IDs.

    .. code-block:: bash

        xxxxx NameNode
        xxxxx SecondaryNameNode
        xxxxx DataNode
        xxxxx NodeManager
        xxxxx Jps
        xxxxx ResourceManager


7. Browse the web interface for the ResourceManager. By default this should be at http://localhost:8088 or if you are
using an Amazon VM instance replace localhost with the actual IP address.

Example
-------

1. Make the Hadoop Distributed File System (HDFS) directories.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hdfs dfs -mkdir -p .
        $HADOOP_PREFIX/bin/hdfs dfs -mkdir input


2. Copy the input files into HDFS. In this example, we use files in $HADOOP_PREFIX/etc/hadoop/ directory as input files.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hdfs dfs -put $HADOOP_PREFIX/etc/hadoop/* input


3. Run the "grep" example provided.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'hadoop'


4. View the output files on HDFS.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hdfs dfs -cat output/*

    Or copy the output files to the local filesystem.

    .. code-block:: bash

        $HADOOP_PREFIX/bin/hdfs dfs -get output output
        cat output/*

    You should see the output as follows.

    .. code-block:: bash

        167     hadoop


Stop daemons
------------
If you are done, you can stop all daemons by using this code:

    .. code-block:: bash

        $HADOOP_PREFIX/sbin/stop-dfs.sh
        $HADOOP_PREFIX/sbin/stop-yarn.sh
