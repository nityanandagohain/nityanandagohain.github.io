---
id: 1
title: "RTLS"
subtitle: "Building a realtime messaging system using redis Pub/Sub"
date: "2021.06.13"
tags: "distrbuted-systems, redis, backend, go"
---

We want to build a message streaming system that is very highly scalable, where users can join a stream and able to receive messages in near real-time. The users may be connected to servers distributed across different regions. An example of this kind of system will be the messages that you see on Instagram live, they may not be exactly synced with the video but they are real-time.

For simplicity purposes, we are not considering historical messages and persisting messages for future use. Though it's pretty simple to add once we implement this architecture.

## While designing this system will consider these requirements
1. Users can join a stream and receive messages.
2. Users can send messages if they want.
3. Users can be distributed across different servers across different regions.
4. Users should be able to receive messages in near real-time.


## Architecture details

!["architecture"](https://user-images.githubusercontent.com/26831659/121815611-fd30ec00-cc94-11eb-8c44-ce0fd7287dba.png)


* Our clients will establish a long-lived HTTP connection with the server and the server will send new messages to the client through **SSE**(server-side events). (For simplicity purposes we have **assumed** that there will be a few users who will be adding messages back to back compared to most of the users who will just view the messages realtime and occasionally add/reply messages in the stream, we can switch to WebSockets if it's required).
* Our backend servers will connect to the Redis cluster and use the pub/sub feature of Redis. Redis cluster supports high performance and scalability of up to 1000 nodes using its gossip protocol. More on Redis cluster [https://redis.io/topics/cluster-spec](https://redis.io/topics/cluster-spec)
* When a stream starts we create a Redis channel, and whenever a new message is added we add that to the Redis channel.
* The clients who are connected to the servers will be subscribed to the specific Redis channel, so as soon as a new message is published it will be sent to all the clients using SSE.

## Yay that's it we built a highly scalable messaging system
We can use this architecture for a variety of systems where near real-time communication is necessary with a little bit of modification.

The implementation in go can be found here [https://github.com/nityanandagohain/rtms](https://github.com/nityanandagohain/rtms)

Feel free to shoot me any questions at nityanandagohain@gmail.com