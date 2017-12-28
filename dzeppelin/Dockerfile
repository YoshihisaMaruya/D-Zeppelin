FROM ubuntu:16.04
MAINTAINER RV <m0029.swim@gmail.com>

# ARG and ENV
## JAVA
ARG JAVA_MAJOR_VERSION=8
ENV JAVA_HOME /usr/lib/jvm/java-${JAVA_MAJOR_VERSION}-oracle

## R
ENV R_HOME /usr/lib/R

## Spark
ARG MAJOR_HADOOP_VERSION="2.7"
ARG SPARK_VERSION="v2.1.0"
ENV SPARK_HOME /spark

## Zeppelin
ARG ZEPPELIN_VERSION="v0.7.3"
ARG ZEPPELIN_SPARK_VERSION="2.1"
ARG ZEPPELIN_HADOOP_VERSION="2.7"
ARG ZEPPELIN_SCALA_VERSION="2.11"
ENV ZEPPELIN_PORT 8080
ENV ZEPPELIN_HOME /zeppelin
ENV ZEPPELIN_CONF_DIR $ZEPPELIN_HOME/conf
ENV ZEPPELIN_NOTEBOOK_DIR $ZEPPELIN_HOME/notebook
ENV ZEPPELIN_INTP_CLASSPATH_OVERRIDES $ZEPPELIN_HOME/myconf

# Change Timezon
RUN echo "Asia/Tokyo" > /etc/timezone

# Install System Tools
RUN \
  sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get -y upgrade && \
  apt-get install -y build-essential && \
  apt-get install -y software-properties-common && \
  apt-get install -y byobu curl git htop man unzip nano wget && \
  rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get -y install libncursesw5-dev libgdbm-dev libc6-dev zlib1g-dev libsqlite3-dev tk-dev libssl-dev openssl libbz2-dev libreadline-dev
RUN apt-get update && apt-get install -y apt-file libfontconfig npm maven apt-transport-https
RUN apt-file update

# Install Java
RUN \
  echo oracle-java${JAVA_MAJOR_VERSION}-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
  add-apt-repository -y ppa:webupd${JAVA_MAJOR_VERSION}team/java && \
  apt-get update && \
  apt-get install -y oracle-java${JAVA_MAJOR_VERSION}-installer && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/oracle-jdk${JAVA_MAJOR_VERSION}-installer

# Install Node for zeppelin build
RUN npm cache clean
RUN npm install n -g
RUN n stable
RUN npm install yarn -g
RUN npm install bower -g
RUN npm install npm-run-all eslint eslint-config-standard eslint-plugin-import eslint-plugin-promise eslint-plugin-standard eslint-config-google

# Install Python and packages
RUN \
  apt-get update && \
  apt-get install -y python python-dev python-pip python-virtualenv && \
  rm -rf /var/lib/apt/lists/*
RUN pip install --upgrade pip
RUN pip install numpy
RUN pip install matplotlib
RUN pip install tensorflow
RUN pip install keras
RUN pip install pandas
RUN pip install scikit-learn
RUN pip install edward

# Install R packages
RUN echo "deb http://cran.rstudio.com/bin/linux/ubuntu xenial/" | tee -a /etc/apt/sources.list && \
    gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9 && \
    gpg -a --export E084DAB9 | apt-key add - && \
    apt-get update && \
    apt-get install -y r-base r-base-dev
RUN R -e "install.packages('devtools', repos = 'http://cran.us.r-project.org')"
RUN R -e "install.packages('knitr', repos = 'http://cran.us.r-project.org')"
RUN R -e "install.packages('ggplot2', repos = 'http://cran.us.r-project.org')"
RUN R -e "install.packages('rJava', repos = 'http://cran.us.r-project.org')"
RUN R -e "install.packages('RJDBC', repos = 'http://cran.us.r-project.org')"


# Install spark
RUN git clone  --depth 1 --branch ${SPARK_VERSION} https://github.com/apache/spark.git

WORKDIR spark

ENV MAVEN_OPTS "-Xmx2g -XX:ReservedCodeCacheSize=512m"
RUN ./build/mvn -Pyarn -Psparkr -Pmesos -Phive -Phive-thriftserver -Phadoop-${MAJOR_HADOOP_VERSION} -Dhadoop.version=${MAJOR_HADOOP_VERSION}.0 -DskipTests clean package

# Install Zeppelin
WORKDIR /
# the bower cannot execute at root user. stupid.
RUN git clone --depth 1 --branch ${ZEPPELIN_VERSION} https://github.com/apache/zeppelin.git && \
	cd zeppelin && \
	sed -i -e "s/bower install --silent/bower install --silent --allow-root/g" zeppelin-web/package.json && \
	dev/change_scala_version.sh ${ZEPPELIN_SCALA_VERSION} && \
	mvn clean package -DskipTests -Pspark-${ZEPPELIN_SPARK_VERSION} -Phadoop-${ZEPPELIN_HADOOP_VERSION} -Pyarn -Ppyspark -Psparkr -Pr -Pscala-${ZEPPELIN_SCALA_VERSION}

CMD ["/zeppelin/bin/zeppelin.sh","start"]
