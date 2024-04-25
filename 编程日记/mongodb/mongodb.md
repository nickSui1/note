### mongodb简介
- MongoDB is an open source NoSQL database management program. NoSQL is used as an alternative to traditional relational databases. NoSQL databases are quite useful for working with large sets of distributed data. MongoDB is a tool that can manage document-oriented information, store or retrieve information.(MongoDB是一个开源的NoSQL数据库管理程序。NoSQL被用作传统关系数据库的替代方案。NoSQL数据库在处理大型分布式数据集时非常有用。MongoDB是一个可以管理面向文档的信息、存储或检索信息的工具。)
- MongoDB 是一种文档数据库，它所具备的可扩展性和灵活性可以满足您对查询和索引的需求
- MongoDB 本质上是一种分布式数据库，所以高可用性、横向扩展和地理分布都是内置且易用的

### mongodb 安装
- 使用homebrew
  - Tap the 
MongoDB Homebrew Tap
 to download the official Homebrew formula for MongoDB and the Database Tools, by running the following command in your macOS Terminal:<br>
`brew tap mongodb/brew`<br>
If you have already done this for a previous installation of MongoDB, you can skip this step.
  - To update Homebrew and all existing formulae:<br>
`brew update`<br>
  - To install MongoDB, run the following command in your macOS Terminal application:<br>
`brew install mongodb-community@6.0`
 - configuration file<br>
/usr/local/etc/mongod.conf<br>
log directory<br>
/usr/local/var/log/mongodb<br>
data directory<br>
/usr/local/var/mongodb
 - To run MongoDB (i.e. the mongod process) as a macOS service, run:<br>
`brew services start mongodb-community@6.0`
 - To stop a mongod running as a macOS service, use the following command as needed:<br>
`brew services stop mongodb-community@6.0`
 - To run MongoDB (i.e. the mongod process) manually as a background process, run:<br>
`mongod --config /usr/local/etc/mongod.conf --fork`
 - To verify that MongoDB is running, perform one of the following:<br>
If you started MongoDB as a macOS service:<br>
`brew services list`<br>
 If you started MongoDB manually as a background process:<br>
 `ps aux | grep -v grep | grep mongod`
 - Connect and Use MongoDB
To begin using MongoDB, connect 
mongosh
 to the running instance. From a new terminal, issue the following:<br>
`mongosh`<br>
[mogosh document link:] <https://mongodb.com/docs/mongodb-shell/#mongodb-binary-bin.mongosh>