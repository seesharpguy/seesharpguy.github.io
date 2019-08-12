---
layout: post
title: Spark on Kubernetes
subtitle: Step by step for noobs like me
# bigimg: /img/path.jpg
tags: [apache, spark, kubernetes, 2.4.3, scala, soup-to-nuts]
---

Today is a special day for me for two reasons.

1. 17 years ago today, I helped bring into this world my oldest son. Happy Birthday Braden Sites!!!
2. Today marks the day I *finally* wrote my first blog!

> I plan on writing a series of blog posts related to Apache Spark on Kubernetes, for the simple fact it that I don't think it can all be covered in a single post.

Today I want to cover two very well known open source projects — Apache Spark, a framework for large-scale data processing; and Kubernetes. As of version 2.3, Apache Spark comes pre-packaged with native Kubernetes support. Users can run Spark workloads in an existing Kubernetes 1.7+ cluster and take advantage of Apache Spark’s ability to manage distributed data processing tasks. Apache Spark workloads can make direct use of Kubernetes clusters for multi-tenancy and sharing through Namespaces and Quotas, as well as administrative features such as Pluggable Authorization and Logging.

Apache Spark is an essential tool for data scientists, offering a robust platform for a variety of applications ranging from large scale data transformation to analytics to machine learning. Data scientists are adopting containers en masse to improve their workflows by realizing benefits such as packaging of dependencies and creating reproducible artifacts. Given that Kubernetes has become the de facto standard for managing containerized environments, it is a natural fit to have support for Kubernetes APIs within Spark.

Concretely, a native Spark Application in Kubernetes acts as a custom controller, which creates Kubernetes resources in response to requests made by the Spark scheduler. In contrast with deploying Apache Spark in Standalone Mode in Kubernetes, the native approach offers fine-grained management of Spark Applications, improved elasticity, and seamless integration with logging and monitoring solutions. 

## Prerequisites

> This post assumes you already have a working knoweldge of Spark, and have created a Spark executable.

1. Spark executable
2. Kubernetes cluster (I am using docker-for-desktop mac, but minikube will work also)
3. Docker
4. A runnable distribution of Spark 2.3 or above from [here](https://spark.apache.org/downloads.html)

## Create a base image

> I like to create a base docker image in which all of my Spark applications will use. This way I can manage the version lifecycle once, and have each of my applications inherit that version from the base.

Spark comes with a set of tools to create this base image for you. Open your terminal to the downloaded location in step 4 above. Simply run the following commands to create and push your base image to your docker repository.  

```shell
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag build
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag push
```

## Create your spark application in Docker image

Using the base image you just created, build and push your application using the sample Dockerfile  

```docker
FROM your.docker.host/repository/spark-base:2.4.3

COPY yourapplication.jar /opt/spark/work-dir/yourapplication.jar
```

## Execute spark-submit

Now that you have a Spark application hosted in a docker repository, execute the following command to submit the Spark application to kubernetes  

```shell
bin/spark-submit --deploy-mode cluster \
--class com.yourpackage.YourClass \
--master k8s://https://localhost:6443 \
--conf spark.kubernetes.authenticate.driver.serviceAccountName=default \
--conf spark.kubernetes.namespace=default \
--conf spark.kubernetes.container.image=your.docker.host/repository/sparkApplication:tag \
--conf spark.kubernetes.container.image.pullPolicy=IfNotPresent \
--conf spark.kubernetes.driver.pod.name="your-application-pod" \
--conf spark.executor.instances=3 \
local:///opt/spark/work-dir/yourapplication.jar
```

You can monitor your spark application in kubernetes through the dashboard, or by running the following command

```shell
$ kubectl logs your-application-pod
```

I hope my post has helped you execute your first Spark application in kubernetes. I will post additional blogs relating to Spark on Kubernetes soon regarding  

1. Connected to an S3 compliant bucket (including minio)
2. Enabling eventlogging and Spark History Server
3. Publishing to Kafka
4. Landing data into ElasticSearch
5. Dynamic ETL processing with Spark
