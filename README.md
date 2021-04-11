# System-Design-Fundamentals

## Client-Server

DNS query → HTTP request

- **IP address**: A unique identifier for a machine
- **DNS**: Domain Name System query to get IP address
- **Server**: Specified which port to listen（ex. http uses port 80, https uses port 443）

## Network Protocols

### **IP**

- packet = fundamental unit of data that is sent from one to another
- packet = header + data
    1. header usually contains source/destination IP address 
    2. IP version（IPV4 most uses）（IPV6 more and more now）
- Can’t guarantee order of packets

### **TCP**

- build on top of IP
- Can guarantee order of packets（resent .etc）
- packet = header + TCP header + data
- handshake

### **HTTP**

- build on top of TCP
- following request response paradigm
- method, statusCode .etc

## Storage

- Database is just a server.
- store/retrieve
- Different offerings for different use case.

### Persistance

- Usually refers to disk, but in general it is any form of storage that persists even it dies.

## Latency And Throughput

- Some system really cares about latency（ex. video games）
- Accuracy vs Latency?
- They’re not necessary correlated. Don’t make assumptions based on one another.

### Latency

- The time it takes for a certain operation to complete in a system
- Reading 1MB from RAM: 250 microseconds
- Reading 1MB from SSD: 1,000 microseconds
- Transfer 1MB over Network: 10,000 microseconds
- Reading 1MB from HDD: 20,000 microseconds
- Inter-Continental Round Trip: 150,000 microseconds
(a packet from CA -> Netherlands -> CA)

### Throughput

- How much data can it transfer from one system to another in a given time
- Gigabytes per seconds, Request per seconds .etc

## Availability

- high availability comes with trade offs. Something like cost higher latency or lower throughput
- Decide what part of system should be high available

### **Nines**

- 99%（two nines）
- 99.999%（five nines = HA = high availability）

### **SLA/SLO**

- S**ervice-Level Agreement / Service-Level Objectives**
- SLA means we guarantee you this amount of availability
- SLA = bunch of SLOs

### **Redundancy**

- duplicating/multiplying certain parts of the system to eliminate single points of failure
- passive redundancy（如果有一個掛了，沒關係，還有很多個）
- active redundancy（總共有五個，有一個掛了，其他的會知道並接手）
**single points of failure = 掛了整個系統都會掛**

## **Caching**

- Used to reduce or improve latency of a system
- Can occur in different level of system
- Remind staleness（有些重要有些不重要，像是留言重要、觀看數不重要）
- Consider using caching to store immutable/static data
- Consider using caching when single reading/writing data
- 注意資料的一致性

**Scenario**

1. Client Level - Cache to not to do network request
2. Server Level - Cache to avoid doing same heavy operation
3. Database Level - Cache to avoid doing lots of same operation 
（loading some celebrity’s profile）

狀況****

- **client -> server -> database**
- **server has cache**
- **Write through cache**
    - server cache changed & database also changed
- **Write back cache**
    - only update server cache
    - update to database every random seconds .etc
- **Cache Eviction Policy**
    - Least Recently Used = LRU policy
    - Least Frequently Used Policy
    - FIFO
    - or maybe just randomly…
- **Content Delivery Network**
    - A CDN is a third-party service that act like a cache for your servers.
    - Maybe sometime your server only located in one region.
    - CDN has servers all around the world, so it’s much faster to reach
    - also often referred to as PoPs (Points of Presence)
    - Popular CDNs are CloudFlare and Google Cloud CDN

## **Proxies**

- between servers and clients
- client -> proxy -> server -> proxy -> client

### **Forward Proxy**

- Masks client IP（server sees proxy’s IP）
- basically how VPN works

### **Reverse Proxy**

- Masks server IP
- client think it’s talking to the actual server but it isn’t. Instead it gets reverse proxy’s IP.
- filter requests, cache, load balancer .etc

## **Load Balancers**

- A type of reverse proxy that distributes traffic across servers.

### **Server-Selection Strategy**

- Round Robin 用同一種順序輪流跑一遍
- Weighted Round Robin: 一樣輪流，但比較強的server跑比較多次
- Based on performance / load: Load Balancer有某種健康檢查機制，根據狀況分配
- IP Based: Hash client IP address to different server (好處是都會到同一個server，可以做cache .etc)
- Path Based: 不同url path分配到不同server (如果server要有大變動，只會影響到某些功能)
- 滿常會有同一個系統，用很多不同種類的Load Balancer
