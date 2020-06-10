+++
title = "StopCovid app: overview of the backend server code"
description = ""

type = ["blog","post"]
tags = [
    "java",
    "spring boot",
    "covid19",
    "backend"
]
date = "2020-06-10"
categories = [
    "development"
]
[ author ]
  name = "Jérémy Basso"
+++


<p align="center">
<img src="/stopcovid.jpg" style="width:600px"/>
</p>

StopCovid is a mobile application launched by the french government in order to perform contact tracing of COVID-19 infections and inform potential new infected.
The codebase of the application is released publicly [here](https://gitlab.inria.fr/stopcovid19).

I was quite interested in seeing how they have done things because the app has a lot of expectations, and the press about it is quite harsh. One of the questions was the lack of time to implement the app because of the situation.

 I decided to perform a quick overview of the backend part, source code can be found [here](https://gitlab.inria.fr/stopcovid19/robert-server), and share it with you.
 
I am not an backend development expert (yet), so expect not to agree with me on some of points ! Also, this article is not an audit of the code, but reflects my personal opinion on what I checked.

## Tech choices

### Language : Java 1.8
```xml
 <java.version>1.8</java.version>
 ```
The language used is Java in 1.8 version, which is a logical choice for an application of this size. We could expect PHP, Javascript on Node.js as well.
 The use of Java 8 can be discussed, since Oracle stopped updating in 2016, and Java 13 has a good adoption so far. Java 8 still works perfectly fine, but my choice would go to a newer version.
Kotlin (still on JVM) was an option as well, but still has less adoption than Java.

### Dependencies management: Maven over Gradle
The dependencies management is handled by Maven. When working with Java projects, the two main possibilities for this subject are Maven and Gradle, and both are totally fine.

While Maven was the first to go out, I personally prefer Gradle because of its API thats seems easier to use. Yet, Maven is a valid choice.

### RPC framework : gRPC and REST

```xml
<grpc.version>1.29.0</grpc.version>  
<protobuf.version>3.11.0</protobuf.version>
```
I totally expected a classic REST or even SOAP API in this backend, and was very surprised to notice the presence of [gRPC](grpc.io). 
This framework allows to exchange requests between servers and clients, based on HTTP/2 protocol and Protocol Buffers. One of its strength is the ability to develop backend services, generate gRPC "interface" contract, and then generate connectors to this interface for any platform or language.

For the record, StopCovid frontend's is native Android and native iOS, so gRPC makes total sense. Indeed, you will only have to develop interface once on the backend-side, and the frontend will only need to generate interfaces from this. This is when gRPC shines : avoid duplicate development and mistakes when dealing with multiple performances. 

Otherwise, gRPC is as well is centered on high-performance, so another good point here.

When diving into code, I found out REST was used. REST is the most used and most standard protocol out there, so nothing to note here.

To sum up, they used gRPC for all the crypto operations around the app, and REST for "classic" API calls.


### Database: PostgreSQL and MongoDB

```xml
<postgresql.version>42.2.12</postgresql.version>
```
I expected that kind of database, which is an industry standard. There are a lot of well known databases out there, my guess would have been PostgreSQL or MariaDB. 

```xml
<dependency>  
 <groupId>org.mongodb</groupId>  
 <artifactId>bson</artifactId>  
</dependency>
```

There seems to be some MongoDB as well, and it is a bit surprising to find multiple databases in the same project, but why not.

Diving into code, MongoDB seems to be the most used database in this project.

### Spring Boot

```xml
<parent>  
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-parent</artifactId>  
 <version>2.2.6.RELEASE</version>  
 <relativePath /></parent>
```
They choose the Spring Boot framework that is very popular, and it is always a good choice. The version is one of the latest, which is good.

## Code

### Architecture 

#### Modules
```xml
<modules>  
 <module>robert-server-ws-rest</module>  
 <module>robert-server-batch</module>  
 <module>robert-server-database</module>  
 <module>robert-server-crypto</module>  
 <module>robert-server-common</module>  
 <module>robert-crypto-grpc-server</module>  
 <module>robert-crypto-grpc-server-messaging</module>  
 <module>robert-crypto-grpc-server-storage</module>  
</modules>
```
The application is divided in 8 modules , which is understandable. Each module has its own dependencies, which is a bit harder to maintain, but a tradeoff for more separation and readability.

As a result, each module has minimal logic implemented.

#### Module structure

Every module share the same core package structure, even though some packages are not needed sometimes. 
The most complete module I based my reflexion on is `robert-server-ws-rest`. Here are the packages and how I understand them :

- `service` and `service.impl`:  business logic (interfaces and implementation
- `utils`: utils classes
- `exception`: exceptions and handlers
- `vo` and `vo.mapper`: entities for web services and mapping from and to business entities
- `dto`: entities for data transfer (as you can guess from the name)
- `controller`and `controller.impl`: controller for API (REST or gRPC)
- `database`: database functions
- `models`: business entities

This structure is pretty simple and does the job. In that kind of structure you do not split into few layers (like Data/Domain/API), but you just split every different type of file by its role, which leads to more packages.

### Code quality

####  Testing

A lot of tests follow the pattern Given/When/Then, which provides a good readability. Yet, the tests seems a bit simple and test only a unit of code. 

I did not find workflow test of the applications (like end-to-end tests), which I personally like since it helps to understand code, and check that the full workflow is working, not only the different parts separately.

It would be interesting to know the test coverage of the backend, my guess is above 90%, since every important function seems to have one test tied.

#### Comments and javadoc

My  general opinion is that sometimes this code lacks of comments. 
There are good comments when something weird is implemented, but there are missing comments about standard stuff, like fields description. Some fields have name like "ebid" or "ecc", and without any comment it is impossible to know what those fields are about.

The project contains 11 TODO's, which is surprising for a released product. Most of them are tied with commented code.

The javadoc around the code is very inequal, it depends on the modules. Some modules contains javadoc on all functions (robert-server-crypto for instance), when other zero (robert-server-database for instance).

When browsing around the code, I was surprised to notice huge amount of commented code.

This if of course bad practice, and I was very surprised to find that in production version of the code. This kind of mistakes is easy to check and remove, I guess time was the missing resource here.
 
#### Magic Numbers

The code contains **a lot** of magic numbers. Magic numbers are variables that contain numbers, initialized in the code instead of stored in a constant elsewhere, as an example :
```java
@NotNull  
@Size(min = 6, max = 36)  
@ToString.Exclude  
private String token;
```
Those numbers 6 and 36 should be stored elsewhere and accessed trough constants. 
Imagine you want to change the size of the token max from 36 to 40. If you change the number below, you could break your code elsewhere because the size 36 would be hard coded somewhere else. By storing it to a constant, you could modify it in one place and the change would be effective everywhere else.

#### General quality 

From my point of view, the overall quality of code is good. Most of the logic is related to crypto functions, and it is difficult to find any issue with those functions without testing it. 

I think what is missing is a tool for quality of code that would help detect bad practices from the start and fix most of the mistakes quoted before (Sonarqube as an example), and probably some time to work with it.

## Conclusion

First of all, it was very interesting to gain access to the code base of the application, and I hope this will become a standard for government projects. It was a great experience for me to check how things are done for a backend of this importance, and I learned a lot.
I find out it is really difficult to properly evaluate a code like this without spending lot of time on it. While it is easy to find basic mistakes, it is very hard to assess how logic is implemented without a complete understanding of the business rules.

To conclude about StopCovid backend, I think most of the choices technology-wise are good. The overall quality of the code is good, but you can notice the lack of time they had to implement it with some bad practices.
 
My point here is not to cast disgrace on the backend development team, I think they did their best with what they had and they can be proud of the result. The issue is more about releasing an app when time is running out and development seems not totally finished.


It seems dangerous to release the StopCovid at this state, because when someone with bad intentions want to break the app, they will manage to, and they did during the bug bounty.

Also, it is interesting to note that the app got released on June 2nd, few days only after opening the code source and the related bug bounty.

At the moment I write (June 10th), the StopCovid app has more than one million downloads, which is huge, even though the interesting metric would be the daily users.

Since this app had a lot of discussions in France, here is some interesting content about other sides of StopCovid :

- [Why StopCovid fails as Privacy-Preserving Design by Nadim Kobeissi](https://nadim.computer/posts/2020-05-27-stopcovid.html)
- [French article about the risks around StopCovid by Paula Forteza](https://medium.com/@paula_forteza/stopcovid-une-efficacit%C3%A9-incertaine-pour-des-risques-r%C3%A9els-7e12b3747cfc)
- [Twitter thread about StopCovid server hosting by Julien Dubois](https://twitter.com/juliendubois/status/1267928958491910146?s=20)  

  

**Thanks for reading !**