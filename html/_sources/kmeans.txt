K-Means
=======

This section describes how to implement the K-means algorithm using Hadoop.

.. image:: images/figures/hadoopkmeans.png
   :height: 300px
   :width: 520px
   :alt: hadoop kmeans
   :align: center


Understanding K-Means
-------------------
K-Means is a very powerful and easily understood clustering algorithm. The aim of the algorithm is to divide a given set of points into "K" partitions. "K" needs to be specified
by the user. In order to understand K-Means, first you need to understand the proceeding concepts and their meaning.

1. Centroids
    Centroids can be defined as the center of each cluster. If we are performing clustering with k=3, we will have 3 centroids. To perform K-Means clustering, the users needs to
    provide the initial set of centroids.

2. Distance
    In order to group data points as close together or as far-apart we need to define a distance between two given data points. In K-Means clustering distance is normally calculated as the Euclidean
    Distance between two data points.

The K-Means algorithm simply repeats the following set of steps until there is no change in the partition assignments, in that it has clarified which data point is
assigned to which partition.

    1. Choose K points as the initial set of centroids.
    2. Assign each data point in the data set to the closest centroid (this is done by calculating the distance between the data point and each centroid).
    3. Calculate the new centroids based on the clusters that were generated in step 2. Normally this is done by calculating the mean of each cluster.
    4. Repeat steps 2 and 3 until data points do not change cluster assignments, meaning their centroids are set.


Pseudo Code
------------------
Denote:

- N is the number of data points
- M is the number of centroids
- D is the dimension of centroids
- Vi refers to the ith data point(vector)
- Cj refers to the jth centroid


------------------
The Main Method
------------------

.. code-block:: java

    generate N data points (D dimensions), write to HDFS
    generate M centroids, write to HDFS
    for iterations{
        configure a job
        launch the job
    }


------------------
The Mapper
------------------

.. code-block:: java

    load centroids

    #The value of the input key-value pair is a data point Vi
    find the nearest centroid Cj for the data point Vi
    Context.write(j, <Vi, 1>)


------------------
The Reducer
------------------

.. code-block:: java

    #The key is an ID of a centroid, the value list is a list of <Vi, 1>
    newCentroid = a new D dimensional vector
    count = 0
    for each pair <Vi, 1> in the value list{
        for k in 0 to (D-1) {
            newCentroid[k] += Vi[k]
        }
        count += 1
    }

    for k in 0 to (D-1) {
            newCentroid[k] /= count
    }

    output newCentroid to HDFS



Compile the Code
------------------
The code is available at https://github.com/ADMIcloud/examples. Download the code by using the git clone command or by clicking the Download Zip button. Then go to the HadoopKmeans directory and compile the code.
In build.xml, change "PATH-TO-YOUR-HADOOP-HOME" to your Hadoop Home directory.

.. code-block:: xml

    <project name="hadoopCompile" default="jar" basedir=".">
        <target name="init">
	        <property name="sourceDir" value="."/>
	        <property name="outputDir" value="classes"/>
	        <property name="buildDir" value="jar"/>
	        <property name="lib.dir" value="PATH-TO-YOUR-HADOOP-HOME"/>
	        <path id="classpath">
		        <fileset dir="${lib.dir}" includes="**/*.jar"/>
	        </path>
        </target>
        <target name="clean" depends="init">
	        <delete dir="${outputDir}"/>
	        <delete dir="${buildDir}"/>
        </target>
        <target name="prepare" depends="clean">
	        <mkdir dir="${outputDir}"/>
	        <mkdir dir="${buildDir}"/>
        </target>
        <target name="compile" depends="prepare">
	        <javac srcdir="${sourceDir}" destdir="${outputDir}" classpathref="classpath"/>
        </target>
        <target name="jar" depends="compile">
	        <jar destfile="${buildDir}/hadoopkmeans.jar" basedir="${outputDir}">
		        <manifest>
			        <attribute name="Main-Class" value="admicloud.kmeans.mapreduce.KmeansMain"/>
		        </manifest>
	        </jar>
        </target>
    </project>


.. code-block:: bash

    $ cd HadoopKmeans
    $ ant



Run the Code
------------------
The usage is:

.. code-block:: bash

    $ hadoop jar jar/hadoopkmeans.jar <num Of Data Points> <size of a vector> <num of Centroids> <number of map tasks> <number of iterations>

For example

.. code-block:: bash

    $ hadoop jar jar/hadoopkmeans.jar 100 3 10 2 3

Hadoop K-means wil firstly generate 100 data points, each a 3-D vector. The data will be saved to HDFS. It then generates 10 initial centroids and writes them to HDFS. For every iteration, K-means loads centroids and reads key-value pairs to do computation, then writes new centroids back to HDFS.


View the Results
------------------

.. code-block:: bash

    $ hdfs dfs -ls -R kmeans

