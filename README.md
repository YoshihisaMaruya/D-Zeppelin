# D-Zeppelin
Apache Zeppelin with Apache Spark + Python(Keras, TensorFlow) over Docker.

Quickstart enviroment for data cleansing, collection, statistics, machine learning and visualization.

# Base Image
ubuntu:16.04

# Include Tools
- Appache Zeppelin v0.7.3 with Spark 2.1 and Hadoop 2.7
- Python 2.7.3 (numpy, matplotlib, tensorflow, keras, pandas, scikit-learn, edward)
and R, node included

# Qucik Start
1. Install docker and docker-compose at your local machine

2. Clone Project
~~~~
> git clone https://github.com/YoshihisaMaruya/D-Zeppelin.git
~~~~

3. Copy and Custom docker-compose.yml/conf/notebook for your enviroment
~~~~
> cp _docker-compose.yml docker-compose.yml # Please custom options for your enviroment
> cp -r dzeppelin/_myconf dzeppelin/myconf # Please add conf file such as hive-site.xml
> cp -r dzeppelin/_conf dzeppelin/notebook # Add your note book
~~~~

4. Build Image and Up docker
~~~~
> docker-compose up -d
~~~~
 
# Links
- [Zeppelin Doc](https://zeppelin.apache.org/docs/0.7.3/) 
- [docker-spark](https://github.com/epahomov/docker-spark)

# Todo
- cude gpu support
 
