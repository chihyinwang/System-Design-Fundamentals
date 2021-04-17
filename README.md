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

## **Hashing**

### Consistent Hashing

- 新增或移除的時候，幾乎可以保證所有的Client依然指到相同的server

### Rendezvous Hashing

- 將client跟server套到某個加權後的算式計算出一個分數 => 得到最適合的server
- 由於有加權過，所以新增/刪除後，大部分的client依然能配對到原本的server

## **Relational Database**

- A very structured database in which data is stored following a tabular format.
- Often supports powerful querying in SQL
- =SQL Database
- 必定遵從ACID

### **Non-Relational Database**

- = Non-SQL Database

### **SQL**

- Structured Query Language
- Load data without loading in memory

### **ACID**

- Atomicity 一個操作裡面有多個小操作時，此操作要成功代表所有小操作都成功
- Consistancy 任何一項操作都不會讓database進入一個不合理的狀態，你的改變我看得見
- Isolation 可能會同時有多個操作觸發，但最後還是一個一個做
- Durability 資料會存在disk裡，不會隨便就不見

### Database Index

- 用來加速搜尋的額外data
- 不過downside是就需要額外的空間來儲存，在存資料時也變得更久

## Key-Value Stores

- 非常有彈性且單純
- 一種 NoSQL Database，常常用在 caching 跟 dynamic configuration
- 知名的有：DynamoDB, Etcd, Redis, and ZooKeeper
- 有分存到 disk 跟 in-memory

### Etcd

Etcd is a strongly consistent and highly available key-value store that's often used to implement leader election in a system.

### Redis

An in-memory key-value store. Does offer some persistent storage options but is typically used as a really fast, best-effort caching solution. Redis is also often used to implement rate limiting.

### ZooKeeper

ZooKeeper is a strongly consistent, highly available key-value store. It's often used to store important configuration or to perform leader election.

## Specialized Storage Paradigms

### Blob Storage

Optimized for storing and retrieving massive amounts of unstructured data

- Blob Store 就是要來解決儲存 blob 的問題，因為一般不會把 blob 存在 SQL Database 裡面
- 在查詢時，與 key-value 很類似，不過不要混淆了，他們是為不同的目的而存在。比如 key-value 無法存取如此大量的資料，且比較著重在優化 latency，而不是 availability and durabilty
- 知名的有：S3 (Amazon), GCS (Google), Azure (Microsoft)

Blob = 任意無結構的data，比如 video file, image file, text file, large binary compiled code, ...，通常資料量、數量都很大

### Time Series DB

A database that is specialized for storing time series data

- 會用在 monitoring，比如說要監控所有發生的事件、IoT 互聯的資料、股價...
- 知名的有：InfluxDB, Prometheus

### Graph DB

主要著重在不同 data set 之間的「關係」

- 有些資料很自然的可以使用 Graph，比如說 social networks, posts
- 這些資料若放在 SQL 裡面，指令會相對複雜，但在 Graph 裡面就不會有這個問題，而且很快
- 查資料就很像在 traversing nodes
- Cypher: 一種 graph query language，原為 Neo4j 開發出來的，漸漸被其他人所使用
- 知名的有：Neo4j

### Spatial DB

儲存與空間有關的資料，像是地理位置、餐廳在地圖上的位置...

- 與一般的 index 不同，他使用 spatial index。很多這些 spacial indices 是 tree-based，像是 R-trees, K-D trees, M-trees...，Quad-tree 也是其中一種。
- 有時候為了避免一直呼叫 API，我們會自己用一個 Quad-tree 去做 caching

Quadtree = 一種有四個 node 的樹，要馬四個要馬沒有。

他會將地圖不斷切成四等份（左上、左下、右上、右下），可以設條件他會切到什麼程度為止，比如說切到格子內只有五個 location 以內。

查詢時只需要 log4 n ，非常快就可以找到想要的 location

## Replication And Sharding

- 示意圖

### Replication

💡 **由多個 database 來備份資料提高 Redundancy 並降低 Latency**

✳️  為什麼需要？

- 如果只有一個 database，如果掛了資料都會不見
- 此時若有另一個備份的 database，就可以解決這個問題

✳️  LinkedIn post example

- 兩個 database，一個在美國、一個在印度，這樣就可以有低的 latency
- 同步（Sync）是指兩邊的資料庫要馬上同步，這個過程通常會比較久，因為兩邊都要寫入，且都要順利完成。基本上你不會想讓你兩邊的資料不同步
- 而非同步（Async）的狀況，會用在不需要及時同步的資料上。比如說發的 post 要出現別人的塗鴉牆上，這種比較沒有緊急性的需求，就可以用 Replication 的 Async 方式來同步（假設在美國發文，且兩個資料庫每五分鐘同步一次，在美國的會馬上看到發文，在印度的會在同步後才看到）

### **Shards**

💡 **切分 data（data partitioning）讓 database 不會過多備份相同資料，並增加更多 throughput**

✳️  為什麼需要？

- 如果今天 request 太多，一個 database 應付不來（throughput不足），這時很自然的會想 scale horizontally，增加更多 database
- 但此時會碰到另一個問題，我們真的想要有一堆一模一樣的 data 嗎？
- 所以我們可以把這些 data 切分，由不同 database 存不同的 data，把一個大 main database 變成更多小 database，這就是 sharding
- 如此可以增加 throughput，也可以避免重複備份過多的 data
- 若要深究，這是一個非常複雜的問題

✳️  怎麼切

- 比如說是 relational database，可以切 tables。比如說帳單資料，名字開頭 A-E 的存一個、F-J 的存一個...
- 怎麼切，就是你的 sharding strategy 是什麼
- 以上面帳單資料為例，XYZ 開頭的 data 可能就會比較少被拜訪，其他的會被多次拜訪（Hot Spot），所以這個 strategy 不夠好。這樣其實就失去了當初用 shard 的意義（每次都拜訪同一個，那跟只有一個有什麼差）
- 我們可以用 hashing 去做

Hot Spot：當工作分配不均時，代表有某些 server 會被拜訪比較多次，稱為 Hot Spot。
（可能是因為 sharding key 或 hashing function 不理想 或 工作本來就 skewed）

✳️  實例

- Client → Server → Reverse Proxy → Shards
- 通常會讓 Reverse Proxy 去處理要找哪個 Shard
- 切不同地區、切不同種類的 data、切不同的 column（only for structured data）

## Leader Election

💡 **如何讓多台機器擁有共同的認知（share their states）**

✳️  實例解說 

- Third-Party Service ← Server ⇆ Database
- Netflix 會員每個月續約的扣款程序，Database 要 Third-Party 去做扣款動作
- 問題：不希望兩個直接溝通（Database 有風險）
    - 在兩者之間放一個 Server
- 衍伸出一個問題：如果那個 Server 掛了怎麼辦？
    - 增加更多 Server
- 衍伸出另一個問題：扣款絕對不能被重複做，那該如何管理這麼多 Server？
    - 讓這幾個 Server 自行選出一個 Leader，由它去做扣款。若 Leader 掛了，剩下的會再次選出一個 Leader，由它去做扣款。
- 問題：這為什麼很難做到？
    - 要讓機器彼此間有共識是很難的
    - 如果是網路壞掉呢？
- Consensus algorithms
    - Paxos
    - Raft
    - ...
- 有一些 Tools 可以幫助我們 implement 自己的 Leader Election
    - ZooKeeper (Uber)
    - Etcd
        - Key-Value stores
        - Highly available & strongly consistent (不多 database 可以同時擁有這兩個特質)
        - Implement The Raft consensus algorithm (這就是為什麼他們可以同時擁有兩個特質)
        - 我們自己用 Etcd 實作，就會讓 Servers 和 Etcd 溝通，從那邊得到 Leader，如此就是實作 Leader Election 了
