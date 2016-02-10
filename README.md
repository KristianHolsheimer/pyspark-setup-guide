#Spark + pyspark setup guide

This is guide for installing and configuring an instance of Apache Spark and its python API pyspark on a single machine running ubuntu 15.04.

-- *Kristian Holsheimer, July 2015*

---

## Table of Contents
1. [Install Requirements](#requirements)

    1.1 [Install Java](#requirements-java)

    1.2 [Install Scala](#requirements-scala)

    1.3 [Install git](#requirements-git)

    1.4 [Install py4j](#requirements-py4j)

2. [Set Up Apache Spark](#spark)

    2.1 [Download source](#spark-tarball)

    2.2 [Compile source](#spark-compile)

    2.3 [Install files](#spark-install)

3. [Examples](#examples)

    3.1 [Hello World: Word Count](#examples-helloworld)
  
---

In order to run Spark, we need Scala, which in turn requires Java. So, let's install these requirements first

<div id='requirements'/></div>
##1 | Install Requirements

<div id='requirements-java'/></div>
###1.1 | Install Java

```bash
$ sudo apt-add-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java7-installer
```

Check if installation was successful by running:

```bash
$ java -version
```

The output should be something like:

```bash
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```

<div id='requirements-scala'/></div>
###1.2 | Install Scala

Download and install deb package from scala-lang.org:

```bash
$ cd ~/Downloads
$ wget http://www.scala-lang.org/files/archive/scala-2.11.7.deb
$ sudo dpkg -i scala-2.11.7.deb
```

***Note:*** *You may want to check if there's a more recent version. At the time of this writing, 2.11.7 was the most recent stable release. Visit the [Scala download page](http://www.scala-lang.org/download/all.html) to check for updates.* 

Again, let's check whether the installation was successful by running:
```bash
$ scala -version
```
which should return something like:
```bash
Scala code runner version 2.11.7 -- Copyright 2002-2013, LAMP/EPFL
```

<div id='requirements-git'/></div>
###1.3 | Install git

We shall install Apache Spark by building it from source. This procedure depends implicitly on git, thus be sure install git if you haven't already:
```bash
$ sudo apt-get -y install git
```

<div id='requirements-py4j'/></div>
###1.4 | Install py4j

PySpark requires the `py4j` python package. If you're running a virtual environment, run:

```bash
$ pip install py4j
```
otherwise, run:
```bash
$ sudo pip install py4j
```

<div id='spark'/></div>
##2 | Install Apache Spark

<div id='spark-tarball'/></div>
###2.1 | Download and extract source tarball
```bash
$ cd ~/Downloads
$ wget http://d3kbcqa49mib13.cloudfront.net/spark-1.6.0.tgz
$ tar xvf spark-1.6.0.tgz
```
***Note:*** *Also here, you may want to check if there's a more recent version: visit the [Spark download page](http://spark.apache.org/downloads.html)*.

<div id='spark-compile'/></div>
###2.2 | Compile source
```bash
$ cd ~/Downloads/spark-1.6.0
$ sbt/sbt assembly
```

This will take a while... (approximately 20 ~ 30 minutes)

After the dust settles, you can check whether Spark installed correctly by running the following example that should return the number π ≈ 3.14159...
```bash
$ ./bin/run-example SparkPi 10
```

This should return the line:
```bash
Pi is roughly 3.14042
```

***Note:*** *You want to lower the verbosity level of the log4j logger. You can do so by running editing your the log4j properties file (assuming we're still inside the `~/Downloads/spark-1.4.0` folder):*
```bash
$ cp conf/log4j.properties.template conf/log4j.properties
$ nano conf/log4j.properties
```

*and replace the line:*

    log4j.rootCategory=INFO, console

*by*

    log4j.rootCategory=ERROR, console

<div id='spark-install'/></div>
###2.3 | Install files
```bash
$ sudo mv ~/Downloads/spark-1.6.0 /opt/
$ sudo ln -s /opt/spark-1.6.0 /opt/spark
```

Add this to your path by editing your bashrc file:
```bash
$ nano ~/.bashrc
```

Add the following lines at the bottom of this file:
```bash
# needed for Apache Spark
export SPARK_HOME=/opt/spark
export PYTHONPATH=$SPARK_HOME/python
```
Restart bash to make use of these changes by running:
```bash
$ . ~/.bashrc
```

If your ipython instance somehow doesn't find these environment variables for whatever reason, you could also make sure they are set when ipython spins up. Let's add this to our ipython settings by creating a new python script named `load_spark_environment_variables.py` in the default profile startup folder:
```bash
$ nano ~/.ipython/profile_default/startup/load_spark_environment_variables.py
```
and paste the following lines in this file:
```python
import os
import sys

if 'SPARK_HOME' not in os.environ:
    os.environ['SPARK_HOME'] = '/opt/spark'

if '/opt/spark/python' not in sys.path:
    sys.path.insert(0, '/opt/spark/python')
```

<div id='examples'/></div>
##3 | Examples

Now we're finally ready to start running our first PySpark application. Load the spark context by opening up a python interpreter (or ipython / ipython notebook) and running:

```python
>>> from pyspark import SparkContext
>>> sc = SparkContext()
```

The spark context variable `sc` is your gateway towards everything sparkly.


<div id='examples-helloworld'/></div>
###3.1 | Hello World: Word Count

Check out the notebook [spark_word_count.ipynb](spark_word_count.ipynb).
