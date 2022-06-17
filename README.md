##  Application Design and Implementation
As illustrated in the below figure, we use two docker container groups that work independently and share the same network.
One is for the background Kafka service (zookeeper + kafka borker) and another is for our application (producer + consumer).

![image](https://user-images.githubusercontent.com/76958080/174341118-0fef3cd0-1210-4cae-a789-3cedf054374d.png)
We implement our app containers as independent modules such that each of them has its own directory with a Dockerfile. 

## Kafka container
In this container group, we use zookeeper and kafka images (broker) from Confluentinc. The broker listens on port 9092  and connect to the zookeeper through port 32181

## Application container
We implement our application followed the single-event processing design pattern such that the pruducer writes transaction events into one topic and the consumer reads events in that topic and uses a helper function to classify the event type (legit/fraud). To connect our app container group with Kafka container group, we use the same network configuration in docker-compose file and connect our app to kafka borker through enrionment variable

## How to run

Step 1: Build and start the kafka container

'''docker-compose -f docker-compose.kafka.yml build && docker-compose -f docker-compose.kafka.yml up'''


Step 2: Build and start our application container (generator and detector)

'''docker-compose build && docker-compose up'''


Test the consumer using built-in kafka-console-consumer

'''docker-compose -f docker-compose.kafka.yml exec broker kafka-console-consumer --bootstrap-server localhost:9092 --topic streaming.transactions.fraud
docker-compose -f docker-compose.kafka.yml exec broker kafka-console-consumer --bootstrap-server localhost:9092 --topic streaming.transactions.legit'''
