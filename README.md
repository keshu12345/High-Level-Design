# High-Level-Design

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-high-level-design">About High Level Design</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
  </ol>
</details>

# High Level Design Baisc 
we have to keep in mind of all these basic when we are designing any system 
![Alt text](image-1.png)

## DNS (Domain Name Server)
    ISP-internate service provider
    DNS Resolver
     #:53 port
     #GeoDNS
     #ISP
     #Local Cache 
     #MultiplesIP
     1. Root Name Server(RN)
     2. Top Level Domain Server (TLD NS)(take Doamin Detail and return ANS'IP)
     3. Authoritative Nameserver (ANS) (take Domain IP? and return 12.23.34.45)

     Low Letancy application :
     Caching is required 
     we can cache on::
     * BroLser Level
     * OS level
     * ISP Level
     * DNS Server
     
     User in india and DNS is in USA then resolving the DNS domain take 900ms mean we have 900ms lag in request so avoid the lag we have disributes DNS in all the Geographically Location



![Alt text](image.png)

## Load Balancing

### 1. Load Balancer
  ####  Stateful Loadbalancing
   * All the request with same ID must be routed to same server
   *  Load should be uniformly distributed.
   We can add and remove the machine and evenly distributed the load using mod
  
  ```
  N-Number of server
  ID%N=server_id
  N=3
  11%3=2
  21%3=0
  10%3=1
  repectively route toward server
  ```
   


### 2. API Gateway

### 3. CDN (Static content)

 Global external Application Load Balancer or the classic Application Load Balancer 

 Cloud CDN works with the global external Application Load Balancer or the classic Application Load Balancer to deliver content to your users. The external Application Load Balancer provides the frontend IP addresses and ports that receive requests and the backends that respond to the requests.
 * Cloud CDN
  [follow this docs](https://cloud.google.com/cdn/docs/overview#:~:text=Cloud%20CDN%20works%20with%20the,that%20respond%20to%20the%20requests.)


  ## How will store large size files??
   around 50TB
   
   1. Store the file
   2. System should be Reliable (it should not lost)
   3. Download the file
   4. Analyties

   Divide the 50TB file in chunck (c1,c2,c3,c4,c5)
   divide the file chunck of 1MB
   ```
   No of chunks = 50TB/1MB = 50*1MB*10^6/1MB 
   = 50*10^6 chunks
 what is issue to recieve too many part of chunks??

 Issues:
 1. Collating the chunck(concates the chunck there is overhead)
 2. Cost of entires
   ```

  ### HDFS Hadoop Distributed File System

  default size of HDFS to divide into chunks is
  ```
  HDFS 2.0  128MB
  HDFS 1.0  64MB

  table is maintaing in  Name Node Server(Only one NNS in whole HDFS cluster that is replicated)
  Name Node Server -->this is source of truth
  file1:chunk1->meta-data-1
  file2:chunk2->meta-data-2

  Why 128MB chunk??
  These system is design for certain type of operation
   store large file
   download large file
   some analytices
   These do cerntain type of benchmarking of each chunk
   File size is increase peoples are using large file so they revise the default size 128MB for comman operation

   if size of chunk is too big, retries will be expensive since they discard partial output?

   There is DATA NODES it have original data like
   data node1   data note2     data node3
   chunk1        chunk2         chunk3
   chunk3        chunk1         chunk2      replication

   replicate data synchronusly from Name Server Node to DATA NODE parallely
   If every file is written into DATA NODES then return SUCCESS
   This is follow MASTER SLAVE ARCHITECTURE
  ```
![Alt text](image-2.png)

### Rack Awareness
Most of us are familiar with the term Rack. The rack is a physical collection of nodes in our Hadoop cluster (maybe 30 to 40). A large Hadoop cluster is consists of many Racks. With the help of this Racks information, Namenode chooses the closest Datanode to achieve maximum performance while performing the read/write information which reduces the Network Traffic. A rack can have multiple data nodes storing the file blocks and their replica’s. The Hadoop itself is so smart that it will automatically write a particular file block in 2 different Data nodes in Rack. If you want to store that block of data into more than 2 Racks then you can do that. Also as this feature is configurable means you can change it Manually. Example of Rack in a cluster

![Alt text](image-3.png)
![Alt text](image-4.png)

#### Rack awareness policies

* There should not be more than 1 replica on the same Datanode.
* More than 2 replica’s of a single block is not allowed on the same Rack.
* The number of racks used inside a Hadoop cluster must be smaller than the number of replicas.
   
  More Details :
 [Rack Awareness](https://www.geeksforgeeks.org/hadoop-rack-and-rack-awareness/)

 #### Who does the devision part??
 Clent is sending stream of data in byte if reached to 128MB that is send to Name Node Server it is make chunk of it and store to into Data Node server

 ![Alt text](image-5.png)

 What if file size is 200MB??
 * Then one chunk 128MB and another chunk is 72MB
 
 We do not aspect low latency


 ## Nearest Neighbors
  Problem Statement :
  * Find the nearest X number of neighbors
  * How will find the restaurent to minimum numbers of reaturant of particular radius
  ### Brute Force
   Find all resturant of the world and their latitude and longitude  and my latitude and longitude
   ```
   (x1,y1)-->(x2,y2)
   square root of |(x1-x2)|^2+|(y1-y2)|^2

   find all the distance of all resturant and find top 10 nearest distance

   It will take lots of time
   ```
### MYSQL
   suppose places store in MYSQL DB latitude and longitude store in it issue besause double is 8 byte later we solve the bit issue 
   Ractangle approach to make some sence 

   ![Alt text](image-6.png)
   
Pontial issue 
 * How will find the right K??
 * Query time query take lots of time ,
 double  does not have enough amount of precision will able to single latitude and longitude
 * If broken down latitue and longitude in multiple parts then do not have issue
 * If you don not pin point yours location it mean you do not have enough precision.It mean your location 100 meter above from booked location
 * Why precision issue happend because by default  all decimal number infinit in nature
 * How many number exist decimal point number exit bewtwen 0 and 1  it is infinit numbers
 * Useing 8 bit we can represent 256 combination numbers (2^8) but in between 0 and 1 infinit numbers of number possible
 Here there is precision issue 
 ![Alt text](image-7.png)

#### solve issue of precsion
There is 
```
lat repesent hypotheically
 lat*10^9
 max Latitude 180
 180.(9 places decimal)
  ( 9 places decimal represnt into)-->lat_int
  after this what will be remaining decimal represnt 
  lat_float
```

![Alt text](image-8.png)


## Approach 3
Break entire world into grids

![Alt text](image-9.png)

This is look very good therotically  but grid is too small and exiting location not come into this g
grid than call othere more grids then query become slow 

Better approach if area is dense the take small grid if area is parse then grid area take bigger this better than breaking etire world into grid

* Design veriable size of grid 


#### Devide the entire world into grids(Not equal size) so that every grid have approx 100 points How I do that??

Storing grid in MYSQL super easy
  
  SOLUTION OF ABOVE QUESTION IS QUADTREE

  ## QUAD TREE

  ```
  Every Node to ask do you have more than 100 points location if have then divide into 4 parts it will be recursivelly
  
  Hight of the Quad Tree O(log N)
  N-Numbers of places that is started with
  ```

  ![Alt text](image-10.png)

  ![Alt text](image-11.png)
  ![Alt text](image-12.png)
  ![Alt text](image-13.png)

  *  How will create the Quad Tree
  *  Given (X,Y) How will find the gridID and neighbour gridID
  ```
  1. Find the place of given (X,Y) and find gridId
  2. Find all the sibling using Next Pointer
  ```
  
  *  Add new place (X,Y)
 ```
  Find the place , check if the current node has > 100 points, if so, split it. Or else just add in the current node
 ```
  *  Delete existing place
  ```
  Frst find the place  and check after deleting point  their points less then 100 points then delete all the sibling go back to root
  else delete only point
  ```


 In Real world  Where do I store the quad tree??

 ```
 1. System is read heavy system
 2. Very high availabilty rather than consistency
 ```

 There is 100 millions places can we store into single machine or not
 How much store needed to store 100 millions palces




 # Uber System Design

 * How do we store quad tree??
 * How to Quad Tree design for Uber
 * There is 100 millions places do we require single machine or multiple machine to store

 100 millions places stroed exactly once

 How many many nodes there is in this tree

```
 leaf node =100 millions
 parent of leaf node =100 millions/4
 parent of parent node=100 millions/16
 1 millions=10^6

10^8+10^8/4+10^8/16+10^8/64.............infinity
r=1/4

total nodes=10^8*(1/1-r)=10^8*(1/1-1/4)=10^8*4/3
totals nodes=10^8*(3/4)
```

![Alt text](image-14.png)
![Alt text](image-15.png)

Totals nodes=6.5 millions
* In the worst case ,all places in the 4th level
* Some level shorter and some level larger
* we store co-ordinate or placeId something and meta data is stored into seperately in DB

* for leaf the data structure is { top left, bottom right, place_ids[] } ?

```

type Node Struct{
  topLeft*Node
  bottonRight*Node 
  4 Points {childrens nodes}
}

64 byte { topLeft*Node(32 byte)
  bottonRight*Node (32 byte)}
  because precison is double double --that is 16 byte

1 Poniter --> 4 bytes
4 Poniter--> 16 byt

 total byte:=80 byte


 space require::
 6.5 millions*80 byte+100 millions*32 bytes
 ~ 4000 millions byte = 4GB
 This fit in single machine in RAM
 

```

Uber is using same Quad tree but little bit complicated because location is moving

How Uber is work??

Intercity this is diffrent product we are not building this -> this is for different city and same city

Intracity same city

```
Requirement::

1. User take the Cab
2. Cabs 

Cabs have two state 
  1. Available to hire
  2. Unavailable(somebody taken the cab,I am driver and I am goen mark unavailable)

  Usecase:
    User at(Lat,Lun)
    match me with nearest avaiable cab

    (Notification goes in round robin 
    to these cabs here is rider 
    do you want to accept )->Yes/No

    10 Millions of Cabs 

    best sharding Key is City

    In city wise best case cabs 
     50000 Cabs

```

I am at location (X,Y) of city I want nearest cabs

#### Worst Case Solution 
* Brodcast all the cabs and filter in cabs level if you have to show or not
* Brodcasting the cabs is very very expensive because every single time lots of person requesting the cabs 
* you are braodcasting messages to 50k  diffrent cab ids may be avaible that point of time .it is very expensive

####  Dynamically changing Location Solution
* cab ping the location is avaible every time  using websocket
```
Recent Loction  car_id-->(X,Y)
```
```
Lets have 
In-memory  Map
Redis
Key     Value
cab--> most recent known location

cab is sendin g location every 1 minutes if they are active and moving 
```

#### From Driver Side app optimization

```
send location 
1. only when my location has changed in last 1 minutes
2. If the driver app knew . the grid part of instead of rely on Quad Tree
2. If driver know the gridId and also know the gridId boundary and if it is moving within the boundary it's not need to update Quad Tree , only problem is moving beyond the boundary

3. Beyound the gridId then my location is changed then send to in memory Map this is my new gridId
   Operations happends after that 
    1. Delete old one point in gridId when changing location
    2. Add new one point in gridId when changing location
```

![Alt text](image-16.png)

Do we need to create child node immidately or wait some time??

```

Suppose every grid contain 50 cabs it is thresold


Range 50-75 cabs
if I am ok if cabs is reaches 51  because cabs are moving but if cabs 75 then we have break further in 4 parts
```

![Alt text](image-17.png)

![Alt text](image-18.png)
```
Periodically ,I update grid dimenssion if needed

Probably It have  per hous, per 2nd hour,per 3rd hour

grid split in 4 children
4 children merge into a grid

1 Hash Map
cabId--> last known location
if location is change but gridId is not change then update only HashMap

2. Quad Tree 
carId-->gridId ,neighbourGridId

if gridId is change the update in QuadTree and also update in HashMap
```



# Design Hotstar

This is injestion pipeline

Step1: One person is uploading content on Hotstar there is multiple view consuming by user

![Alt text](image-19.png)

I AM GOING TO DISCUSSING THE INJESTION PIPLINE

### Requirements

```
1. Admin user must be able to upload the content
2. End user filter the content by genre,language

multiple step invole in jensetion pipeline eg whenever I upload the vedios there is auto generated thumbnail,I am only uploading to higher resolution quality qualities so I want to from stem generate lower resolution quality

3. Generate lower resolution vedios and thumbnail
4. Apply geo restriction
```



Therse is user uploading vedios to central server to a DB

![Alt text](image-20.png)

### Scale

```
1. Numbers of vedios available on Hotstar is 1 million
2. Avg size of the each raw vedio file arround to be 1GB
```

### Storage 

```
 storage =1Million*1GB
         = 10^6*1GB
 storage =1 Peta Byte=1PB

 what is 1PB storage data ?? 
 This is binary data (raw data )
 is it querable formate ??
 it is never be query on this data

 So this data is no querable data  that is store in something like file system in S3 also strore in CDN

 store in --> S3 or CDN

 what is amount of storage require??
 in Database 1 million of metadata require to store 
 
 Suppose  1 entry of meta data taking 1 KB stoarge then

 Total storage in DB = 1 Million*1KB=1GB

 1GB data easly come in one Machine it don't want to require multiple machine it iseasly fit one machine easily

 Lets evaluate the RDBMS system for storage in DB
```

### Schema

Entities

```

1. User -->uploading vedios
2. Vedios--> metadata,resulations,language 
3. Genre(Comedy,Horror,Thriller)
4. Language-> Hindi,Hnglish,spanish,German
5. Country

What kind of realtion between these above enties

User has one to many vedios (1:n)

Vedio has many to many Genre (n:m)

there is another table require 
Vedio_Genere
id --> not require because the locality of reference
vedio_id
genere_id

-->id is not require because locality of reference

-->What is primary key that decide the physical ordering in hard disk

-->fetching the vedios it is faster if be do not include the id fetching through vedio_id beacuse it is decide the storage in hard-disk beacause of it is primary key

Vedio has many to many Language (m:n)

vedio_language
vedio_id
lang_id

Vedio has many to many Country (n:m)

vedio_county
vedio_id
country_id

```

![Alt text](image-21.png)

### Design Goals

Before you answer wether we would want AP system,CP System,AC System, Look at the what the number we got  amount of data we want to store in RDBMS it is 1GB

--> 1GB Easily fit in one machine

Data can fit in one machine  lets evaluate 
```
CAP 

C-consistency
A-Availability
P-Partitioning
```

#### CA System

Data can fit in one machine  so we do not neet of partitioning

What is implication of CA??

Data would be thier in one Node
* What is problem entire data lieing in one node  
* If we are replicating the data into another node that mean we are including the paritioning
* Parition does not mean that is only the sharded data
* Partition mean that data is residing more than one machine and it has to synchronize between two server call parition
* That mean we can can replicate becase this partition

```
CAP does not work for single server 
if more than one server tha apply  CAP Theorm
```

My concern in CA system it has one server if it's die then it Single Point Of Failure. 
```
If this database is down then Hotstar user unable to see the content
```
Availability does not take in accont server crashes

```
Whole crux of CAP Theorm , 
Whole CAP Theorm does not take into account
if your system is down for eg turn off your cluster

I do not Availability
I do not have Consistency
I do not have  whole things
basically I do not have anythings

CAP Theorm based on the assumption 
Entire system can not be malfunction
```
Avoid single point of failure we need replication

What kind of replication ours use case would have??
is it work with eventual consistency or need of strong consistency

* Eventual consistency will work reason that we do not need stron consistency becuase Rate of TPS entry is made very low since quite low .there is very less change to corrupted to any kind of deplay

So here we have paritioning CA system does not work 

Thre is need AP Model because it is evventual consitency

Here AP system model will work

![Alt text](image-22.png)

### Actual Design
Most Bruteforce 

There is client calls to upload API ,upload API transfer the file metadata to server  we send everything the data to Database and file send to S3

![Alt text](image-23.png)

TPS is quit low because not every day admin uload the vedios

What is pros of this approch??
* it is simple 

Demerit :
* We will inroduce the very very bulk code base.
* Parallel excution is not there.

##### Client upload the vedio
when client uplaod the vedios there is multiple step


```
step 1: Check the error (corner copy right voilation)(it's take seconds)

step 2: Whether that vedios uplaod to perticular geography location (it's take minutes)

step 3: Generating lower resolution (this compute intensive task it will take hours)

step 4: manually process and manually approve by somebody (it's take minutes)

step 5: it's take seconds


```
Each step take varying amount of time 

* This is very lengthy process there is multiple steps involve  
 * There might be manual step as well

 ![Alt text](image-24.png)

 If it is taking hours to complete  it can not keep file into memory ,every task is done then send  particular status to client

 needless to say this can be Sync API it has to be Async API 

 if this is going to be Async API ,can I make use of this Async status  an does make sence ton add status field what is status of particular file 

 ##### Aysnc

 ```
 step 1: Uplaod to S3
 step 2: Update database with S3 URL
 ```

 One more problem  step 1: is fail,then we have uplaod the vedio again

 there would be network wastege then offload the uplaod diretly to client in S3


 insead of sending request to client send directly request to S3 , we are saving the bandwidth to Server that is client 

 ![Alt text](image-25.png)

 How will do this ??

1. Creating an API 
```
1. API : singedURL
```

######  SingedURL
client call to server the server generate what is called tokenizedUrl  This tokenizeUrl have access and time expiry attached to its

It have token we can give any who want to call particular API given time frame ,it have TTL
When uplad to S3 and tell to server I have loaded into S3

```
tokenizedUrl: {
              token 
              ttl
              }
```

```
2. API :: Start processing call (Async)
```

If second API is Async then we can introduced the some Queue here

