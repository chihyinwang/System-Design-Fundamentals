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
