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

## Peer-To-Peer Networks

問題：如何一次傳送龐大的資料給很多機器？

✳️  思路：

1. 一個一個輪流傳。但是這樣要傳很久，太慢
2. 增加 Server 來傳。但這樣會有很多相同的資料在這些機器上
3. 將資料切成很多小份，傳給不同接收者，讓接收者們彼此溝通，最後拼湊出完整的資料 ✅
- 圖示

✳️  如何讓 peer 知道下一個要找溝通？

1. Tracker
    - 有一個管理 Server，告訴每個 peer 要找誰
2. Gossip protocol / Epidemic protocol
    - 每個 peer 會有一些 “其他 peer 有什麼資料” 的資訊
    - 其實就跟平常在傳八卦、或是像病毒在傳遞一樣
    - peerA 跟 peerB 溝通，彼此交流他們有的資訊，就會知道下一個要找誰
    - Distributed Hash Table (DHT)： 一堆 hash table 在 mapping
    - Kraken：一個由 Uber 開發出的 p2p 系統

## Polling And Streaming

問題：如何 real-time 更新資料，像是監控溫度、訊息傳送...等等？

### Polling

💡 **讓 Client 定時向 Server 發出 request 更新資料**

- 可是定時還是有延遲，一直縮減間隔會造成 Server 的負擔

### Streaming

💡 **透過 socket 讓兩台機器有一個 open connection，彼此不用再一直發 request**

- Server 從被動的角色，變成主動向 Client 傳送資料（pushing）

根據狀況來判斷要用 Polling 還是 Streaming。若是不用即時更新可用 polling，若是需要即時更新可用 Streaming。

## Configuration

### Static Configuration

- 與程式碼綁定，所以若要改動需要更版（shipped with code）
- 好處：在發布前會有嚴謹的 code review process、tests 去驗證跟檢查
- 壞處：發佈時間久、不彈性

### Dynamic Configuration

- 不與程式碼綁定，通常會需要一個 Database，Application 跟這個 Database 詢問 Configuration
- 比較複雜，最好要有 review process、access control 來保證更動時不會有錯（review process 比 deploy 時間還長）
- 好處：使用起來很彈性、很快，比如說可以用在 UI、一些設定...
- 壞處：沒有流程會檢查這個，也沒有測試會跑

## Rate Limiting

限制一段時間內只能做多少事

- DoS attack（Denial of Service）、D-Dos attack（Distributed Denial of Service）

    ✳️  為什麼 D-Dos 到今天還是很難處理？

    - 他使用多台機器，我們難以辨認是否是同一個使用者，很難去做 rate limit
- Rate Limit on User、IP address、region、entire system...
- 保護系統被攻擊的最終方式
- 通常不會直接在處理 request 的 Server 上做 Rate Limiting，會在另一個 Server 或 Load balancer 或 Database 去處理
- Redis（Key-Value store Database），比如說在收到 request 時先去 Redis 檢查
- Tier Based：可以同時有很多條件（0.5 秒一次＋10 秒內三次＋1 分鐘內十次）

## Logging And Monitoring

問題：如何處理一些初次碰到、難以複製的問題？

### Logging

💡  把 logs（有用的資訊）存到 Database 裡面提供給開發者 debug

- Stackdriver（Google）

### Monitoring

💡  視覺化呈現系統的各種指標

1. 利用 logs 畫出圖形
    - 被 logs 限制，這些 logs 一定要有我想呈現的圖形資料
    - 如果有天要改變 logs，可能會造成 metrics system 出錯
2. Time-Series Database
    - Grafana
    - Pair with a Alerting System 會很不錯（出錯時會警告你）

## Publish / Subscribe Pattern

 問題：如何 scale Streaming（像是股票資訊）
 
 ### Publisher

- 發送訊息到 Topics

### Topics

- 像是一個 Channel，一個中繼站
- 讓 Subscriber 去 Subscribe，監聽是否有新資訊
- 其實就是一個 Database，儲存 Publisher 傳的資料，再把這些資料傳給監聽自己的 Subscriber
- 不同的 Topics 通常也會儲存不同的資料（Separate of Concerns）
- 傳送給 Subscriber 至少會傳送一次（可能會傳很多次，傳送失敗之類的）
- 傳送的這些資料必需是 Idempotent，意思是不論重複做多少次，結果都是一樣的（例如：更改某個狀態，而不是 counter++）
- FIFO
- 有的可以 Replay / Rewind 那些進入 Topics 的資料

### Subscriber

- Subscribe Topics，持續監聽
- 收到資料後會傳 ACK (Acknowledgement) 給 Topics，讓 Topics 知道傳送成功，自己被 listen
- 可根據不同需求去增加 filter

## 📌 MapReduce

問題：如何有效對 Distributed File System 的 Datasets 進行操作、處理、輸出？

✳️  注意

1. 當我們討論 MapReduce，我們就是假設在討論 Distributed File System，它有一個中央管理的地方，知道 MapReduce 的過程與狀況。
2. 我們是把 map function 傳到 dataset
3. key-value pairs structure 是很重要的，如此才能去做 reorganized
4. handle faults / handle failures（比如 network partition, machine failures, ...）
    - 如果有哪個環節失敗了，central control plane 會要求再做一次

### 🔷 Steps

1. Map
    - 把 dataset 變成 key-value pairs
    - 為了避免移動龐大的 datasets，我們一般是把 map function 傳到 dataset 的機器去跑
2. Shuffle
    - 重新整理這些 key-value pairs 到不同的機器（keyA, keyB → mac1, keyC → mac2）
3. Reduce
    - 把 key-value pairs 轉（歸類）成自己想要的輸出
- 示意圖

    ![System%20Design%20Fundamentals%2061ac6ecd6c374463a13f5beb3e87bec9/MapReduce.jpeg](System%20Design%20Fundamentals%2061ac6ecd6c374463a13f5beb3e87bec9/MapReduce.jpeg)

✳️  成果

- 將流程簡化，工程師只需要考慮 input & output（map & reduce function）

    （不需要考慮 fault-tolerance, parallelization of tasks, ...，這些已經被 MapReduce implementation 做完了）

✳️  範例

- 從 Youtube 資料中找到每個 user 的觀看數或按讚數
- 從整個系統的 logs 中尋找一段時間內每個 service 的 log 數

### 🔷 Distributed File System

- Extremely large-scale persistent storage
- 大量的資料被切分成許多塊，存在不同機器裡（Large data set being split up into chunks）
- Central control plane 負責決定 data chunks 要分配到哪裡、怎麼讀資料、如何溝通 ...
