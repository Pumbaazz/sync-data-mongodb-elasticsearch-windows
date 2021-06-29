# Bulk data from mongodb to elasticsearch

1) Cài GO
https://golang.org/dl/
2) Cài Git(có hay không thì cũng được, có thể dùng Power Shell thay thế)

3) Tạo directories

Mở Git Bash shell lên, tốt nhất nên ở C:\ cho dễ thao tác

```
mkdir go
```

sau đó cần set cái GOPATH enviroment variable

```
export GOPATH=`pwd`/go/
```

Tiếp tục thì thực hiện các dòng sau thì thực hiện tuần tự:

```
cd go
mkdir pkg bin src
cd src
mkdir github.com
cd github.com
mkdir compose
cd compose
```

Nếu như GO có báo các dạng lỗi như `go init` ở cuối thực thi các command thì thực hiện theo y như lỗi hướng dẫn

4) Clone transporter

```
git clone https://github.com/compose/transporter
```
 5) Build transporter

 ```
cd transporter
go get -a ./...
go build ./cmd/transporter
```
Nếu như GO có báo các dạng lỗi ở cuối thực thi các command thì thực hiện theo y như lỗi hướng dẫn

Cuối cùng, vào `Edit system Enviroment variables`, chọn Path, thêm vào đường dẫn của `C:\go\src\github.com\compose\transporter` vào 

6) Tạo index elasticsearch

Thực hiện:
```
curl -X PUT "localhost:9200/{tên-index}?pretty"
```

Nếu có bị lỗi `cURL in power shell windows 8.1: “A drive with the name 'localhost' does not exist”` thì thực hiện command `remove-item alias:\curl` trong Power Shell ở quyền Administrator


7) Tạo pipeline transporter

Tại vì ở đây chúng ta sử dụng MongoDB và Elasticsearch nên thực hiện init pipeline như sau:

```
transporter init mongodb elasticsearch
```

8) Chỉnh sửa file pipeline

File pipeline mặc định sau khi tạo sẽ có dạng như sau
```
var source = mongodb({
  "uri": "${MONGODB_URI}"
  // "timeout": "30s",
  // "tail": false,
  // "ssl": false,
  // "cacerts": ["/path/to/cert.pem"],
  // "wc": 1,
  // "fsync": false,
  // "bulk": false,
  // "collection_filters": "{}",
  // "read_preference": "Primary"
})
var sink = elasticsearch({
  "uri": "${ELASTICSEARCH_URI}"
  // "timeout": "10s", // defaults to 30s
  // "aws_access_key": "ABCDEF", // used for signing requests to AWS Elasticsearch service
  // "aws_access_secret": "ABCDEF" // used for signing requests to AWS Elasticsearch service
  // "parent_id": "elastic_parent" // defaults to "elastic_parent" parent identifier for Elasticsearch
})
t.Source("source", source, "/.*/").Save("sink", sink, "/.*/")
```

Ở đây chúng ta cần phải sửa lại 2 chỗ đó là `MONGODB_URI` và `ELASTICSEARCH_URI`

`MONGODB_URI` là URI tới database trên mongodb
`ELASTICSEARCH_URI` là URI tới index trên elasticsearch

Sau khi sửa xong thì có dạng như sau:

```
var source = mongodb({
  "uri": "mongodb://127.0.0.1:27017/test"
})
// Elasticsearch configuration
var sink = elasticsearch({
  "uri": "http://localhost:9200/test"
})
// Get data, transform it, Then save it
t.Source("source", source, "/.*/").Save("sink", sink, "/.*/")
```

9) Cuối cùng
Thực hiện command bắt đầu sync data từ mongodb sang elasticsearch

```transporter run pipeline.js```

Done!
