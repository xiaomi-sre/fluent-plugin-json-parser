# fluent-plugin-json-parser
json parse for json string, only support two nested.

# Installation
```
cp out_record_reformer.rb /etc/td-agent/plugin/
cp parser.rb /etc/td-agent/plugin/
```
# usage
```
<source>
  type tail
  format /^(?<client_ip>[^ ]*) \[(?<time>[^\]]*)\] (?<jsonstr>.*)$/
  time_format %d/%b/%Y:%H:%M:%S %z
  pos_file /tmp/test.pos
  path /tmp/test.log
  tag test
</source>

<match test.**>
  type json_parser
  key_name jsonstr
  tag newtag
</match>
<match newtag.**>
  type elasticsearch
  host 192.168.0.1
  port 9200
  path /
  logstash_format true
  utc_index false
  flush_interval 3s
  include_tag_key true
  tag_key tag
  num_threads 5
  logstash_prefix test_json
</match>
```
# testing
## input
```
127.0.0.1  [19/Oct/2015:15:16:22 +0800] {"id":7457","header":{"code":0,"exe_time":"0.0571","info":"success"}}
```
## output
```
{"client_ip":"127.0.0.1","jsonstr":"{\"id\":\"7457\",\"header\":{\"code\":0,\"exe_time\":\"0.0571\",\"info\":\"success\"}}","id":"7457","code":0,"exe_time":"0.0571","info":"success","tag":"newtag","@timestamp":"2015-10-19T15:16:22+08:00"}
```
