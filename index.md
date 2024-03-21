---
title: Home
---

<div align="center">
    <img src="./images/logo_workshop.png" width="500" alt="Workshop logo">
</div>

# Automated Machine Learning from Your Database System with MySQL HeatWave Cloud Service


Welcome to the official website for our workshop at the [Applied Machine Learning Days Conference 2024](https://www.appliedmldays.org/)!

This workshop is designed to guide you through the seamless integration of automated machine learning capabilities with your MySQL database system, leveraging the powerful MySQL HeatWave Cloud Service.


## Workshop Overview

MySQL HeatWave is a fully managed service, developed and supported by the MySQL team at Oracle. This cloud service allows you to create and manage a MySQL DB System with a HeatWave Cluster to use with services on the cloud.

MySQL HeatWave is the only fully managed database service that combines transactions, analytics, and machine learning services into one MySQL Database. It also includes MySQL HeatWave Lakehouse, which lets users query half a petabyte of data in object storage-in a variety of file formats, such as CSV, Parquet, and export files from other databases.

We propose in this workshop, to display the capabilities of Heatwave via a step by step guide from storing unstructured data and loading it into the Database with Heatwave Lakehouse to applying Automated Machine Learning on that same data.
We will guide the participants though a real case example and allow them to interact by themselves with the service. They will be able to create their database system, load some data and train their own Machine Learning models.
The audience will learn about a range of fully automated machine learning capabilities to solve business problems such as forecasting demand, detecting fraudulent transactions, predicting upcoming maintenance needs, and recommending new products to their customers-all via an easy-to-use, performant, secure SQL/GUI interface.

HeatWave AutoML includes everything users need to build, train, deploy, and explain machine learning models within MySQL HeatWave, at no additional cost. It automates the machine learning lifecycle, including algorithm selection, intelligent data sampling for model training, feature selection, and hyperparameter optimization-saving data analysts and data scientists significant time and effort. Aspects of the machine learning pipeline can be customized, including algorithm selection, feature selection, and hyperparameter optimization.


<div class="toc" markdown="1">
## Contents:

{% for lesson in site.pages %}
{% if lesson.nav == true %}- [{{ lesson.title }}]({{ lesson.url | absolute_url }}){% endif %}
{% endfor %}
</div>

