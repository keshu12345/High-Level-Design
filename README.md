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

![Alt text](image.png)

## Load Balancing

### 1. Load Balancer

### 2. API Gateway

### 3. CDN (Static content)

 Global external Application Load Balancer or the classic Application Load Balancer 

 Cloud CDN works with the global external Application Load Balancer or the classic Application Load Balancer to deliver content to your users. The external Application Load Balancer provides the frontend IP addresses and ports that receive requests and the backends that respond to the requests.
 * Cloud CDN
  [follow this docs](https://cloud.google.com/cdn/docs/overview#:~:text=Cloud%20CDN%20works%20with%20the,that%20respond%20to%20the%20requests.)
 