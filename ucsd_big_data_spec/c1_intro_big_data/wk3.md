# Basic Scalable Computing Concepts

We're going to start diving into the details of Hadoop. Before we do that, we need to understand core concepts which will help us understand Hadoop better. 

## What is a Distributed File System? 

When computers first emerged, information was stored in punch cards that were stored in physical file cabinets. The need to store information in files comes from the need to store info in the long-term. This allows information to persist after the process terminates. This also allows you to store large amounts of info that we can't store within the program or computer memory. Once the data is in a file, multiple processes can access the same info as needed. 

We store files on a hard disk and they're managed by your OS. How the OS manages files is called a file system. How the information is stored has major impact on efficiency and speed of operations. 

Most users work on a single machine with a single hard drive. The user is thus limited to the capacity of their hard drive. What if you have too much data to fit on your hard drive. One way to solve this is to have an external hard drive; or buy a bigger disk. Both are a bit of a hassle. 

Imagine you having two computers and storing some of your data in one and the rest of your data in another. You might want to store you work data in one machine and your personal data in another. Distributing data in multiple computers might be an option, but it raises new issues. You need to know where to find the files you need. 

This situation can be handled by a distributed file system. Assume there are racks of these computers distributed across a wide area network. Datasets or parts of datasets can be replicated across the nodes of a DFS. Since data is already in these nodes, and we need to analyze the data in each node, computation can occur within the node itself. 

Data is often replicated across many regions to make the system more fault tolerant. If one goes down, the data can be found elsewhere. Replication also helps with scaling access to this data by many users. This distributed situation, though, can mean it's difficult to maintain changes to the data over time. However, in most DFS, the data is written once, and then _changes_ to the data are stored as additional data. 

## Scalable Computing Over the Internet

Most computing is done on a single compute node. If the computation needs more than one node, or parallel processing, we use parallel computers. A **parallel computer** is a very large number of single nodes with specialized capabilities connected via a network. E.g. the Gordon Supercomputer has 1024 compute nodes with 16 cores each. This type of computer is costly compared to the **commodity cluster**.

The term **commodity cluster** refers to affordable, parallel computers, with an average number of computing nodes. Not as powerful as traditional parallel machines and are often built of less specialized nodes (more generic). Computing is done on commodity clusters over the internet as distribute dcomputations.

In commodity clusters, the computing nodes are clustered in racks connected to each other via a fast network. There might be many such racks in extensible amounts. Computing in one or more of these clusters across the internet is called **distributed computing**. This enables **data parallellism**. In this paradigm, many jobs that share nothing, can work on different datasets or pieces of data simultaneously (job-level parallelism). We'll refer to this as data-parallelism. 

Large volumes and varieties of big data can be analyzed in this model, achieving scalability, performance, and cost reduction. 

There are many points of failure in such systems. A node or rack can fail, connectivity to a rack can fail, connections between nodes can break, etc. It's not practical to completely restart everytime a failure happens. The ability to recover from such events is called **fault-tolerance**. 

Two solutions emerge: redundant data storage and data-parallel job restart. 

## Programming Models for Big Data

Cost-effective commodity clusters together with advances in DFS to move computation to data provide a potential for conducting scalable big data analytics. How to take advantage of this? 

A **programming model** is an abstraction or machinery. It's a set of abstract runtime libraries and programming languages that form a model of computation. It can be low-level or high-level. 

If the enabling infrastructure for big data analysis is DFS, then the programming model should enable the programmability of operations within the DFS. Being able to write programs that work efficiently on top of DFS for big data. 

Big Data Model Requirements: 
1. Support common big data operations (e.g. split large volumes of data; quickly access data; distribute computations to nodes; etc.). 
2. Handle fault tolerance (e.g. replicate data partitions; recover files when needed). 
3. Enable adding new resources / racks to take advantage of distributed resources at scale (scaling out). 
4. Optimized for specific data types. Should enable operations over a particular set of data types (e.g. graph, doc, key-value, etc.).

A common model is **MapReduce**: a programming model that supports all the requirements we mentioned. It also abstracts out the details of administration, like monitoring, load balancing, etc. 

# Getting Started with Hadoop

The Hadoop frameworks/apps have several overarching themes/goals.

1. Provide scalability to store large volumes of data on commodity hardware.
2. Handle fault tolerance. 
3. Optimized for a variety of data types. 
4. Faciliatate a shared environment. Since even modest sized clusters can have many cores, it's important to allow multiple jobs to execute simultaneously.
5. Provide value for your enterprise. Ecosystem includes a wide range of open sourced projects backed by a large community. These projects are free to use and easy to find support for. E.g. Hive, Pig, Spark, Flink, Cassandra, etc. 

The main components of Hadoop are MapReduce (programming model for processing big data), YARN (scheduler / resource manager), and HDFS.

## The Hadoop Ecosystem

Yahoo created Hadoop in 2005. In the following years, other frameworks and tools were released as open source projects. These provided different features. Today, there are >100 OS projects for big data. Many, but not all, rely on Hadoop.

The Hadoop Distributed File System (HDFS) is the foundation for many big data frmeworks since it provides scalable storage, fault tolerance, etc. As the size of your data increases, you can add commodity hardware to HDFS to increase storage capacity. 

YARN schedules jobs flexibly and also handles resource management over HDFS storage. YARN is used at Yahoo to schedule jobs across 40,000 servers.

MapReduce is a programming model that simplifies parallel computing. Instead of dealing with complexities of synchronization and scheduling, you only have to give MapReduce two functions: Map and Reduce. Google previously used it for indexing websites.

MapReduce only assumes a limited model to express data. Hive and Pig are two models on top of MapReduce to augment data modeling of MapReduce with Relational Algebra and Data Flow Modeling, respectively. Hive was created at Facebook to issue SQL-like queries using MapReduce in HDFS. Pig was created at Yahoo to model data flow based programs using MapReduce. 

Thanks to YARN's ability to manage resources on not just MapReduce, but other programming models as well, Giraph was built by Facebook to analyze the social graphs of its users. Storm, Spark, and Flink, were built for real-time and in-memory processing of big data on top of the YARN resource scheduler and HDFS. This allows you to run big data apps even faster, achieving 100x performance in some tasks. 

HBase, Cassandra, and MongoDB are NoSQL platforms for things like key-values, sparse tables, etc. 

Running all these tools requires a centralized management system. Zookeeper was buitl to perform these duties to wrangle services named after animals. 

All these tools are open source. You can mix and match to get only the tools to achieve your goals. Alternatively, there are several prebuilt stacks using these tools to make this all easier for you: e.g. cloudera, MAPR, and HortonWorks. 

## HDFS

The HDFS is a storage system for big data. It is the foundation for the Hadoop ecosystem. It provides scalability to large datasets and reliability to cope with hardware failures. HDFS allows you to store and access large datasets. 

HDFS achieves scalability by splitting large files across nodes for parallel access. Typical file size is GB to TB. The default chunk size is 64 MB, but you can configure this to any size. 

By splitting the file across many nodes, the chances are increased that one of the nodes keeping a file will fail. HDFS replicates file blocks on different nodes to prevent data loss. So if a node fails, you can find what you're looking for in another one. By default, it keeps 3 copies of every block. You can configure this. 

HDFS is also designed to handle a variety of data types. To read a file in HDFS, you must specify the input file format. To write a file, you must provide the output format. Text files can be read line-by-line, or a word at a time. Geospatial data can be read as vectors or rasters. Etc. 

Two key components of HDFS: 

1. NameNode: for metadata.
2. DataNode: for block storage. 

These operate with a master-slave relationship where the name node issues comments to data nodes across the cluster. The name node is responsible for meta data and data nodes provide block storage. Usually 1 name node per cluster. Usually one data node per machine in the cluster. 

In some sense, the NameNode is the administrator of the HDFS cluster. It keeps track of file names, locations in the directory, and mapping of contents on Data Node. 

DataNode stores file blocks and listens to NameNode for block creation, deletion, and replication. Replication provides [a] fault-tolerance; and [b] data locality, which allows you to pull data from the location closest to you. 

Locality is important because we want to move computation to data, and not the other way around. Recall that the default replication factor is 3. A higher replication factor means increased protection from faults and better data locality. But it also is more expensive and complicated to create more copies. 

## YARN: Resource Manager for Hadoop

YARN provides flexible resource management for Hadoop and extends Hadoop to enable multiple frameworks such as MapReduce, Giraph, Spark, and Flink. YARN sits just above HDFS, interacts with applications, and schedules resources for their use. It enables running multiple apps over HDFS, increases resource efficiency, and allows you to go beyond the MapReduce model.

The original Hadoop stack had no resource manager. It could only run MapReduce jobs and had poor resource utilization. YARN = Yet Another Resource Negotiator. 

Adding YARN between HDFS and the applications enabled many different systems to be built focused on different types of big data applications (e.g. Giraph for graph data, Spark for in-memory analysis, etc.). YARN does so by providing a standard framework that supports custom app dev in the Hadoop ecosystem. It lets you use the tools you think are best for your data.

The **Resource Manager** controls all the resources and decides who gets what. The **Node Manager** operates at the machine level and is in charge of the single machine. Togehter, the Resource Mgr and Node Mgr form the data computation framework. Each application gets an application master. It negotiates resources from the resource manager and it talks to node manager to get its tasks completed. 

YARN is what led to the explosive growth of applications across the Hadoop ecosystem. YARN gives you many ways to extract value from data. It lets you run many distributed applications over the same Hadoop cluster. It also reduces the need to move data around and supports higher resource utilization, resulting in lower costs. 

## MapReduce

MapReduce simplifies creating parallel programs. It was the default programming model for Hadoop. It relies on YARN to schedule and execute parallel processing over the distributed file blocks in HDFS. Hive has a SQL-like interface that adds capabilities to MapReduce. Pig is a high-level data flow language that adds capabilities that help with process map modeling.

Parallel Programming requires expertise in concepts like locks, semaphores, monitors, threads, message passing, shared memory, etc. This is a high learning curve that makes things difficult and is error prone. MapReduce greatly simplifies running code in parallel so you don't have to deal with these issues. 

You only need to create a Map and Reduce tasks. It's based on functional programming: f(x) = y. y depends on x. You provide an operation on Map and Reduce and the runtime executes it over the data.  

* Map: apply operation to all elements.
* Reduce: summarize operation on elements.

For map, the operation is applied on each data element. For reduce, the operation summarizes data in some manner.

"WordCount" reads 1 or more text files and counts the number of occurrences of each word in these files. The output will be a text file with a list of words and their occurrence frequencies in the input data. 

THe first step is to run a map operation on each node. Map is called for each line in the input, on each node individually (A, B, C, D, etc.). Map creates a key-value pair for each word and their count in each line. Map goes to each node and computes rather than the data going to map. We're bringing computation to the data. 

Next, all the key values output from map are sorted based on their key. The key-values with the same word are shuffled to the same node. Next, the reduce operation executes on these nodes to add values for key-value pairs with the same keys. (apple, 1) and (apple, 1) becomes (apple, 2).

The result of reduce is a single key pair for each word in the input file. 

In the map step, each partition of the data gets processed 1 line at a time. We also see parallel grouping of data in the shuffle and sort phase. The parallelization is over the intermediate products. After this grouping, the reduce step gets parallelized to construct one output file. The data gets reduced to a smaller step at each step.

MapReduce is bad for: 
* Frequently changing data. MapReduce would be slow for this since it reads the entire input dataset each time. 
* Dependent tasks. MapReduce requires that computations operate in parallel, independent from one another. 
* Interactive analysis: it doesn't return any results until the entire analysis is finished. 

## When to Reconsider Hadoop? 

Problems that are Hadoop friendly involve future anticipated data growth and long-term availability of data. It can provide quick access to old data, which would otherwise go on long-term storage drives. Other items include scnearios when you want to use multiple apps over the same data store. Others include high volume and high variety. 

Small datasets are not appropriate. You don't need Hadoop for that. 

Hadoop is good for data parallelism. Task parallelism is the simultaneous execution of many different functions on multiple nodes across the same or different datasets. If your problem has task level parallelism, you must do further analysis on what tools you plan to deploy in the Hadoop ecosystem. Proceed with caution.

Certain advanced algorithms are not tenable for Hadoop either. Don't think about throwing away your existing data infrastructure in favor of Hadoop. Hadoop may not be the best data store solution for your business case. 

HDFS stores data in blocks of 64 MB or larger. You may have to read an entire file to pick a single data entry. That makes it a bit harder to perform random data access. 

## Cloud Computing

Cloud is on-demand, anytime, anywhere. Cloud allows you to focus specifically on your proble. Transforming computing infrastructure into a commodity. Now developers don't have to deal with the infrastructure. It's a rental service for computing.

Should you build hardware/software yourself? Or should you rent these resources from the cloud? 

If you build it yourself, it'll be more work, you'll have to pay for hardware, processors, networking, storage, upgrades, etc. This also means management and handling over time and the real esttae cost of keeping the hardware. It's also hard to estimate the size of your hardware needs. It's hard to get this right. Software stacks are also complex and difficult to keep up with. This all leads to high initial capital investmetns, operation of multiple departments, etc. 

Using the cloud, you pay as you go and benefit from quick implementation. You don't have to deal with many of the aforementioned issues. You can deploy apps on servers geographically close to your client. It also solves the resource estimation problem. Cloud lets you handle all this with a click and without a huge capital investment. 

## Cloud Service Models

There are many levels of services you can get from cloud providers. Applications, platforms and infrastructure. 

* Applications: monitoring, content, collaboration, communication, etc. 
* Platform: object storage, identity, runtime, queues, databases. 
* Infrastructure: compute, block storage, networking.

Infrastructure as a Service (IaaS): Bare minimum rental service. You install and maintain OS / app software. You handle platforms and applications.

Platform as a Service (PaaS): the user is provided with an entire computing platform, which could include the OS, programming languages you need, databases, web servers, etc. You can develop and run your own apps/software on top of these layers. 

Software as a Service (SaaS): is the model in which the cloud service provider takes responsibilities for the hardware and software environment completely. This means you can work on using the application to solve your problem.

The decision on which service depends on skills, capital, demand, and security. 

## Value from Hadoop and Pre-Built Hadoop Images

Assembling your own stack from scratch can be messy and difficult for beginners. Getting pre-built images is similar to buying pre-assembled furniture. You can obtain a ready-to-go software stack with a pre-installed OS, required libraries, and application software. This is enabled using virtualization software (e.g. VMWare or Oracle VirtualBox). 

Your stack comes as a large file. Your virtualization platform is a place for the software to run. HortonWorks and Cloudera are two companies who provide this. We're going to use Cloudera in this course. 