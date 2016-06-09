Pseudo-distributed Mode
=======================

Setup passphraseless ssh
---------------------------

Firstly, check with the following command

.. code-block:: bash

    $ ssh localhost

If you cannot ssh to the localhost without a passphrase, use the following commands to setup passphraseless ssh:

.. code-block:: bash

    $ cd ~/.ssh
    $ ssh-keygen -t rsa
    (hit enter to all the options)
    $ cat id_rsa.pub >> authorized_keys


Configuration
---------------------------

Modify the following files, replace $HADOOP_HOME with your own hadoop home path.

In $HADOOP_HOME/etc/hadoop/hadoop-env.sh, replace ${JAVA_HOME} with your own Java home path. If it's ~/software/jdk1.8.0_91, then

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

1. Format the file system

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


The log is in the $HADOOP_LOG_DIR directory (defaults: $HADOOP_HOME/logs)

3. Check if the daemons are started successfully

.. code-block:: bash

    $ jps
    xxxxx NameNode
    xxxxx SecondaryNameNode
    xxxxx DataNode
    xxxxx Jps


4. Browse the web interface for the NameNode. By default it's at: http://localhost:50070

5. Start ResourceManager daemon and NodeManager Daemon

.. code-block:: bash

    $ $HADOOP_HOME/sbin/start-yarn.sh


6. Check if the daemons are started sucessfully:

.. code-block:: bash

    $ jps
    xxxxx NameNode
    xxxxx SecondaryNameNode
    xxxxx DataNode
    xxxxx NodeManager
    xxxxx Jps
    xxxxx ResourceManager


7. Browse the web interface for the ResourceManager. By default it's at http://localhost:8088

Example
---------------------------

1. Make the Hadoop Didtributed File System (HDFS) directories

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -mkdir -p .
    $ $HADOOP_HOME/bin/hdfs dfs -mkdir input


2. Copy the input files into HDFS. In this example, we use files in $HADOOP_HOME/etc/hadoop/ directory as input files

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -put $HADOOP_HOME/etc/hadoop/* input


3. Run the "grep" example provided

.. code-block:: bash

    $ $HADOOP_HOME/bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'hadoop'


4. View the output files on HDFS

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -cat output/*


Or copy the output files to the local filesystem

.. code-block:: bash

    $ $HADOOP_HOME/bin/hdfs dfs -get output output
    $ cat output/*


Stop daemons
---------------------------
If you are done, you can stop all daemons by

.. code-block:: bash

    $ $HADOOP_HOME/sbin/stop-dfs.sh
    $ $HADOOP_HOME/sbin/stop-yarn.sh

