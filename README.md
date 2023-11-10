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
 * How  to  design for Uber
 * There is 100 millions places do we require single machine or multiple machine to store