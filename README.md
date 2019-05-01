# Real Time Analytics Dashboard using SACK (Spark Structured Streaming, Akka, Cassandra, Kafka) Stack and Dash by Plotly an open source real time dashboard

This project contains all the details on how to build Real Time Analytics Dashboard to analyze click stream events.

The real time dashboard displays live incoming clicks aggregated per second.

You can completely build and run this project in AWS/On-Premise. You can run the spark streaming app on  your 
on-premise spark standalone cluster. For ease of use and demonstration purpose, the spark app is 
ran on AWS cluster and I installed zookeeper, kafka, cassandra, and dash on single EC2 instance.

## Setup Instructions

* Spin up an EC2 instance in AWS or on your on-premise
* Follow instructions from kafka_setup.txt
* Follow instructions from cassandra_setup.txt
* Follow instructions from dash_setup.txt
* Spin up AWS wtih Spark 2.3 cluster


## Real Time Data Flow
![Alt Text](https://github.com/aguyyala/real-time-analytics-dashboard/blob/master/data-flow.png)

![Alt Text](https://github.com/aguyyala/real-time-analytics-dashboard/blob/master/real-time-architecture.png)


## Usage

* Run `sbt clean assembly` to build the jar
* scp the jar to EC2 instance and AWS master node.


## Deployment

* SSH into EC2 instance and run the following command to produce some sample click events. `runFor` is a time variable 
in minutes you want it to produce events. 

`nohup java -cp RealTime_Load-assembly-1.0-SNAPSHOT.jar com.indeed.dataengineering.task.kafka.Producer -e=dev --runFor=10 --sleepTime=1000 > kafka.log 2>&1 &`

* Run the following command to initiate the Dash dashboard to see the click stream events flowing to cassandra. You can see
the dashboard on `http://<ec2 instance>:8050/`

`nohup python app.py <ec2-instance> > dash.log 2>&1 &`

* SSH into EMR cluster and run the following command to run Spark Streaming app that consumes events from kafka and perform
real time ETL and dump the raw/aggregated data into Cassandra.

```

spark-submit \
--name RealTime_Load \
--master yarn \
--deploy-mode client \
--driver-memory=10G \
--num-executors=5 \
--executor-cores=3 \
--executor-memory=2G \
RealTime_Load-assembly-1.0-SNAPSHOT.jar -e=prod
```


## Working Prototype

If everything is setup correctly, when the link `http://<ec2-instance>:8050/` is open, you should see the following.

![Alt Text](https://github.com/aguyyala/real-time-analytics-dashboard/blob/master/Live_Click_Count.gif)
