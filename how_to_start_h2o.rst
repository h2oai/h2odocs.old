.. h2o documentation master file, created by
   sphinx-quickstart on Tue Jan  8 15:19:50 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

====================
**How To Start H2O**
====================

H2O is a pure java application.

**Starting a H2O cluster from the command line**

1. Open the terminal program on your computer 

   * Mac Example: Click applications -> utilities -> terminal
   * PC Example: Click start -> all programs -> accessories -> command prompt 

2. Locate your h2o.jar file

   * Note: make sure you have downloaded the latest version of the file
   * File Example: h2o-12.29.10-121812.jar
   * Mac Example: Click Finder -> Downloads -> Look under the date you downloaded the file
   * PC Example: Click start -> your user name at the top -> downloads

3. Input the following command in your terminal window 

   * java -Xmx1g -jar h2o.jar -name mystats-cloud 
   * Note: The h2o.jar should look like this h2o-12.29.10-121812.jar

4. The terminal should respond with the following 

   * java -Xmx1g -jar h2o.jar  -name mystats-cloud -ip 192.168.1.90
   * [h2o] HTTP listening on port: 54321, UDP port: 54322, TCP port: 54323
   * [h2o] (v0.3) 'mystats-cloud' on /192.168.1.90:54321, discovery address /236.151.114.91:60567
   * [h2o] Paxos Cloud of size 1 formed: [/192.168.1.90:54323]

5. You have created a cloud! Open browser to http://localhost:54323/ and start playing with your data.

If you have multiple internet interfaces you will be prompted for -ip

**Shutdown a cloud**

1. http://ip-address:54323/Shutdown

   * Example: wget http://192.168.1.150:54323/Shutdown

2. Also via, kill signal to all pids on all nodes
    
   * Example: $ kill -9 PID

**Launch with HDFS**
  
Parameters: -hdfs -hdfs_version -hdfs_root

1. Input the following command in your terminal window 

   * java -Xmx1g -jar h2o.jar -hdfs hdfs://192.168.1.151 -hdfs_version cdh4 -hdfs_root /datasets 
   * Note: The h2o.jar should look like this h2o-12.29.10-121812.jar

2. The terminal should respond with the following 

   * [h2o] HTTP listening on port: 54321, UDP port: 54322, TCP port: 54323
   * [h2o] (v0.3) 'mystats-cloud' on /192.168.1.90:54321, discovery address /236.151.114.91:60567
   * Nov 28, 2012 3:41:25 PM org.apache.hadoop.util.NativeCodeLoader <clinit>
   * WARNING: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
   * [h2o,hdfs] hdfs://192.168.1.151/datasets loaded 51 keys
   * [h2o] Paxos Cloud of size 1 formed: [/192.168.1.90:54323]

3. Open browser to http://localhost:54323/ and start playing with your data!
  
**Starting Multiple Nodes** 

**Launch with FlatFile of nodes**


H2O can use UDP multicast, which may create traffic on other ports, but that shouldn't matter or occur if you use the **--flatfile** option.
The flatfile is now required for all installations, to avoid any dependence on UDP multicast.
The flatfile must be the same for all invocations, and has this format (the ports match the base address that was given above. In prior versions of H2O, the ip address must be the base address plus 1)

 * /192.168.1.173:55313
 * /192.168.1.174:55313
 * /192.168.1.175:55313

For default ports (HTTP:54321, UDP:54322, TCP:54323) 
Just flat file of ip addresses works:

 * 192.168.1.173
 * 192.168.1.174
 * 192.168.1.175

example:

    * java -Xmx4g -jar h2o.jar -flatfile flatfile
    * [h2o] HTTP listening on port: 54321, UDP port: 54322, TCP port: 54323
    * [h2o] (v0.3) 'hduser' on /192.168.1.151:54321, static configuration based on -flatfile flatfile
    * [h2o] Paxos Cloud of size 1 formed: [/192.168.1.151:54321]
    * [h2o] Paxos Cloud voting in progress
    * [h2o] Paxos Cloud of size 2 formed: [/192.168.1.151:54321, /192.168.1.150:54321

**Other Issues/Arguments**

There are a number of arguments to an H2O jar invocation. All instantiations of the jar must have the exact same arguments.
You should verify what java is installed on your machine. Ideally all machines have the same java and version?

    * java -version

    * java version "1.7.0_09"
    * Java(TM) SE Runtime Environment (build 1.7.0_09-b05)
    * Java HotSpot(TM) 64-Bit Server VM (build 23.5-b02, mixed mode)
    * Example: java -Xmx28G -ea -jar /tmp/h2o.jar --port=55313 --ip=192.168.1.173 --ice_root=/home/0xdiag/ice.55313.1354140499.31 --name=pytest-kevin -hdfs hdfs://192.168.1.151 -hdfs_version cdh4 -hdfs_root /datasets --flatfile=/tmp/flatfile_kevin


**-Xmx28G** specifies a maximum size of 28GB for the java heap. Generally, the default java heap size used by the OS will not be sufficient for any large datasets. It should be set as large as possible, but no bigger than the max dram size minus 1-2GB. If there are other java programs running simultaneously, you should decrease their heap size limits, from the amount you can allocate to the h2o heap. It is okay to have different heap sizes on different h2o invocations, but typically multi-node systems should have machines that are reasonably similar, with similar max heap sizes.

**-name** A unique name for a H2O cloud is required, to avoid joining any existing H2O cloud on the network. Sometimes orphan, so-called "zombie" H2O processes might exist with the same cloud name, due to errors or problems with prior H2O invocations. It's good to verify that no unknown H2O processes exist (using ps on Unix systems) before starting a new H2O.

**-ea** is for enabling java assertions. For reporting potential issues to 0xdata, it's probably useful to use this at all times.

**--ice_root** is the directory where H2O keys will be stored. This can be a lot of data, so should be somewhere with a lot of disk space. (the amount should be some multiple of the datasets you want to model)

You will want to make sure the **--port** is an unused port, and the three addresses covered by --port through --port + 3, are open through any network switches, routers, or OS'es in use. UDP/HTTP/TCP protocol is used.
If you have aggressive firewalling, either hardware or software, you may need to open the ports used. 



If the **-hdfs** arg is used, then the **-hdfs_version** and **-hdfs_root** must also be given.
H2O supports cdh4 and mapr. Other vanilla hadoop hdfs distributions should also work
(UPDATE: what are the exact args?)

**-hdfs_root** is interrogated at h2o boot time, for files that get keys that point to them for possible loading. If the -hdfs arg is used, and the ip address doesn't exist, or the hdfs_root doesn't exist, or there are other problems with hdfs accessiblity, h2o will report problems and not start. 


Not all errors in h2o are currently reported to the browser. Some show up in the stdout and more likely the stderr. Both files should be examined for information.
Contents:

.. toctree::
   :maxdepth: 2



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

