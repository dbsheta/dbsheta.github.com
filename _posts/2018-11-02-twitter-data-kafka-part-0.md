---
title: "Processing Streaming Twitter Data using Kafka and Spark - The Plan"
date: 2018-11-02 23:15:00 +05:30
categories: [kafka, real-time]
excerpt_separator: <!--more-->
---


## What is Apache Kafka?

> Apache Kafka is a publish/subscribe messaging system. It is often described as a “distributed commit log” or more recently as a “distributed streaming platform.”
> Since being created and open sourced by LinkedIn in 2011, Kafka has quickly evolved from messaging queue to a full-fledged streaming platform

<!--more-->
![Source: [https://kafka.apache.org/images/kafka_diagram.png](https://kafka.apache.org/images/kafka_diagram.png)](https://cdn-images-1.medium.com/max/2706/0*f_7HXjtx0Nva3RQR.png)*Source: [https://kafka.apache.org/images/kafka_diagram.png](https://kafka.apache.org/images/kafka_diagram.png)*


<br>
## The Inspiration

I recently read the book [Kafka: The Definitive Guide](https://www.confluent.io/resources/kafka-the-definitive-guide/) by the creators of Kafka. It is truly a wonderful book for anyone who wants to start developing applications with Kafka as well as anyone who wants to know the internals of such a unique platform which is used by most of the Fortune 500 companies.

<br> 
## The Plan

In this series, I’ll be exploring various aspects of Apache Kafka, all by implementing cool data pipeline:

1. We’ll start by setting up a Kafka Cluster in cloud/locally

1. After that, we’ll write a Producer Client which will fetch latest tweets continuously using Twitter API and push them to Kafka.

1. Then, we will implement an app using Kafka Streams API, which will consume the tweets from Kafka in real-time and do basic processing on them like finding number of tweets per user and most used words (i.e word count).

1. We’ll then venture into more cool stuff like writing our own Kafka Connector which will use twitter as data source and learning to use Apache NiFi to achieve the same with less effort.

1. We’ll use Spark Streaming to do sentiment analysis on real-time twitter data

1. Finally, if everything goes well, we’ll try to tweak our architecture and implement Notification service using Firebase and Kafka which will send push notifications to user if his/her tweet has negative sentiment!
 
 <br> 
## Let’s begin!
![[By Amine Rock Hoovr](https://unsplash.com/@hoovr01?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](https://cdn-images-1.medium.com/max/10368/0*U_EwY9N-91IXddnk)
