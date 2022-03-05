# 背景

最近有个小项目需要用到SQLite数据库，go已经有现成的开源库可以实现，但是美中不足的是，开源版本并不支持数据库加密，这个直接在项目中使用，很明显是不行的。

秉承着能不造轮子就不造轮子的原则，在网上搜寻了一下，目前比较有知名度的加密开源实现分别是：wxSQLite3和sqlcipher。其中发现sqlcipher已经有go版本的轮子可以使用了，但是集成后，生成的数据库文件并不能用Navicat打开，到Navicat官网查询后，发现Navicat默认是使用*wxSQLite3* (128-bit AES) 进行加密实现的。为了后续数据查询方便，所以决定用wxSQLite3来实现加密，然而在网上找了许久，都没发现有go版本的wxSQLite3。

最后研究了一下go-sqlite3的实现方式，发现他其实是直接调用C版本的sqlite，那这时候事情就简单，我直接移花接木一下，把里面的C代码替换成wxSQLite3即可，所以就有了现在这个项目。

本项目代码主要是来自以下两个项目：

- [https://github.com/mattn/go-sqlite3](https://github.com/mattn/go-sqlite3)
- https://github.com/utelle/SQLite3MultipleCiphers

# 安装

xxx

# 使用

```Go
key := url.QueryEscape("123456")
dbname := fmt.Sprintf("./foo.db?_db_key=%s", key)
db, err := sql.Open("sqlite3", dbname)
```
目前项目默认使用的加密算法是128-bit AES，如果想切换加密算法，可以修改 wxsqlite3_cgo.go 中 CODEC_TYPE 的值，目前支持以下几种加密算法：

- CODEC_TYPE_AES128
- CODEC_TYPE_AES256
- CODEC_TYPE_CHACHA20
- CODEC_TYPE_SQLCIPHER
- CODEC_TYPE_RC4