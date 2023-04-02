# Table
- [Learning](#learning)
  - [Spanner](#spanner)
  - [Blockstack](#blockstack)
# Learning
## Spanner 
- This article is modified based on <https://zhuanlan.zhihu.com/p/85616742>
- 采用了Megastore的数据模型，Chubby的数据复制和一致性算法，而在数据的可扩展性上使用了BigTable中的技术
- 既具有NoSQL系统的可扩展性，也具有关系数据库的功能
### 数据模型
- Spanner继承了Megastore的设计，数据模型介于RDBMS和NoSQL之间，提供树形、层次化的数据库schema，一方面支持类SQL的查询语言，提供表连接等关系数据库的特性，功能上类似于RDBMS；另一方面整个数据库中的所有记录都存储在 同一个key-value大表中，实现上类似于BigTable，具有NoSQL系统的可扩展性
- 在Spanner中，应用可以在一个数据库里创建多个表，同时需要指定这些表之间的层次关系
- Direcotory: 根节点表中的一条记录，和以其主键作为前缀的其他表中的所有记录的集合
  - Advantages:
    - 一个Directory中所有记录的主键都具有相同前缀.在存储到底层key-value大表时，会被分配到相邻的位置。如果数据量不是非常大，会位于同一个节点上，这不仅提高了数据访问的局部性，也保证了在一个Directory中发生的事务都是单机的。
    - Directory-based shading strategy: 整个数据库被划分为百万个甚至更多个Directory，每个Directory可以定义自己的复制策略。
    - Directory提供了高效的表连接运算方式。在一个Directory中，多张表上的记录按主键排序，交错interleaved地存储在一起，因此进行表连接运算时无需排序即可在表间直接进行归并。
### Transction
- ACID
  - **Atomicity**: transactions can never “partly commit”; their updates are applied “all or nothing”
  - **Consistency**: each transaction `T` transitions the data from one semantically consistent state to another
  - **Independence/Isolation**: all updates by `T1` are either entirely visible to `T2`, or are not visible at all
  - **Durability**: updates made by `T` are “never” lost once `T` commits
- How spanner guarantees?
  - **Atomicity**: the system guarantees atomicity using logging, shadowing, and two-phase commit (2PC)
  - **Consistency**: the application guarantees consistency by correctly marking transaction boundaries
  - **Independence/Isolation**: guaranteed through two-phase locking (2PL) or optimistic concurrency control (OCC)
  - **Durability**: the system guarantees durability by writing committed updates to stable/recoverable storage (e.g., logging, replication, or both)
  
## Blockstack
- This article is modified based on <https://timilearning.com/><https://timilearning.com/lecture-20-blockstack>
### 去中心化架构
在中心化应用程序中，应用程序代码与其存储数据的方式之间存在紧密耦合。 例如，Twitter 应用程序知道如何与 Twitter 的数据库进行交互。 但是在去中心化的应用程序中，我们可以将应用程序代码与用户数据分开。

在这种架构中，我们可以拥有独立于与其交互的应用程序的存储服务。 该服务将基于每个用户而不是每个应用程序存储数据，网络上的每个用户都可以拥有和控制他们的数据。

在设计此架构时，存储服务必须满足以下要求：
- **General-purpose**: similar to the file system on your computer, it must have an API that allows multiple applications to interact with it.
- **Cloud-based**: so users can access their data from anywhere.
- **Fully controllable by a user**: it must support mechanisms for securing the data and controlling who can access it.
- **Supports sharing between users** for apps where one user might read another user's information.

此架构的优点：
- Better ownership and control over their data and how it's used.
- Better security and data privacy, assuming app owners implement end-to-end encryption.
- Improved ability for users to switch between similar applications, since data storage is independent of the applications.

Blockstack design goal:
- Decentralized Naming & Discovery: End-users should be able to (a) register and use human-readable names and (b) discover network resources mapped to human-readable names without trusting any remote parties.
- Decentralized Storage: End-users should be able to use decentralized storage systems where they can store their data without revealing it to any remote parties.
- Comparable Performance: The end-to-end performance of the new architecture (including name/resource lookups, storage access, etc.) should be comparable to the traditional internet with centralized services.
