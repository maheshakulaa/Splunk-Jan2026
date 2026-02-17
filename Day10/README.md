### Sourcetype configs ( props.conf)

-   LINE_BREAKER --> Specifies a regex that determines how the raw text stream is broken into initial events, before line merging takes place.
-   SHOULD_LINEMERGE --> Specifies whether to merge multiple lines into a single event. If set to false, each line is treated as a separate event.
-   BREAK_ONLY_BEFORE --> Specifies a regex that determines where to break the event only before the specified pattern.
-   TIME_FORMAT --> Specifies the format of the timestamp in the log data, which is used to extract the timestamp for each event.
-   TIME_PREFIX --> Specifies a regex that indicates where the timestamp is located in the log data, which helps in extracting the correct timestamp for each event.
-   TZ --> Specifies the time zone for the timestamps in the log data, which is used to correctly interpret the timestamps when they are extracted and indexed.
-   MUST_BREAK_AFTER --> Specifies a regex that determines where to break the event only after the specified pattern.
-   MAX_TIMESTAMP_LOOKAHEAD --> Specifies the maximum number of characters to look ahead in the raw text stream when trying to extract a timestamp, which helps in improving performance when processing large log files.
-   TRUNCATE = <size> --> Specifies the maximum size of an event in bytes. If an event exceeds this size, it will be truncated to the specified size.
-   TRANFORMS --> Specifies one or more transformations to apply to the events, such as masking sensitive data, extracting fields, or routing events to specific indexes or queues based on certain patterns in the log data.
-   REPORT --> Specifies one or more field extraction rules to apply to the events, which can be used to extract specific fields from the log data for easier searching and analysis.
-   EXTRACT --> Specifies a regex to extract fields from the log data, which can be used to create new fields in the indexed events for easier searching and analysis.
-   EVAL --> Specifies a search-time field transformation that evaluates an expression to create a new field or modify an existing field in the indexed events, which can be used for various purposes such as data enrichment, normalization, or custom calculations.


### transforms.conf
-   REGEX --> Specifies a regular expression to match patterns in the log data, which can be used for various purposes such as field extraction, event routing, or data transformation.
-   FORMAT --> Specifies the format for the output of a transformation or field extraction, which can include references to captured groups from the regular expression specified in the REGEX parameter.
-   DEST_KEY --> Specifies the destination key for the output of a transformation or field extraction, which can be a field name, a metadata key, or a queue name depending on the context of the transformation or extraction.
-   SOURCE_KEY --> Specifies the source key for a transformation, which is the field or metadata key that contains the data to be transformed.
-   WRITE_META --> Specifies whether to write the extracted fields as metadata in the indexed events, which can be useful for searching and filtering events based on the extracted fields without having to include them in the raw event data.
-   INGEST_EVAL --> Specifies an evaluation expression to apply to the events during ingestion, which can be used to perform calculations, create new fields, or modify existing fields based on the content of the events.
```
example:

# Index-time evaluations:

[discard-long-lines]
INGEST_EVAL = queue=if(length(_raw) > 500, "nullQueue", "indexQueue")

[split-into-sixteen-indexes-for-no-good-reason]
INGEST_EVAL = index="split_" . substr(md5(_raw),1,1)

[add-two-numeric-fields]
INGEST_EVAL = loglen_raw=ln(length(_raw)), loglen_src=ln(length(source))

# In this example the Splunk platform only creates the new index-time field if 
# the hostname has a dot in it; assigning null() to a new field is a no-op:

[add-hostdomain-field]
INGEST_EVAL = hostdomain=if(host LIKE "%.%", replace(host,"^[^\\.]+\\.",""), null())
```


```
props.conf
[vendor_sales]
SHOULD_LINEMERGE=false
LINE_BREAKER=([\r\n]+)(\[\d{1,2}\/\w{3}\/\d{4}:\d{1,2}:\d{1,2}\:\d{1,2}\])
NO_BINARY_CHECK=true
CHARSET=UTF-8
TIME_FORMAT=%d/%b/%Y:%H:%M:%S
TIME_PREFIX=^\[
TRANSFORMS-myrules = route_to_nullqueue, route_to_apple_index, rename_hostname
SEDCMD-maskpassword = s/(?i)((?:password|pwd|passwd)(?:[\s\=\:]+))(\S+)/\1XXXXXXX/g
SEDCMD-maskpassword = s/(?i)(password[\s\=\:]+|pwd[\s\=\:]+|passwd[\s\=\:]+)(\S+)/\1KKKKKKKKKK/g


transforms.conf
[route_to_nullqueue]
REGEX = CODE\=A
DEST_KEY = queue
FORMAT = nullQueue

[route_to_apple_index]
REGEX = Code\=F
DEST_KEY = _MetaData:Index
FORMAT = apple

[rename_hostname]
REGEX = .*
DEST_KEY = MetaData:Host
FORMAT = uf01

```

## HEC:
```
inputs.conf
[http]
disabled = 0
port = 8443

[http://apple_hec]
disabled = 0
host = ip-172-31-47-239.ap-south-1.compute.internal
index = apple
indexes = apple
token = be3cc297-83bf-4554-9280-5ef8fb98fddb

[http://apple_withack]
disabled = 0
host = ip-172-31-47-239.ap-south-1.compute.internal
index = apple
indexes = apple
sourcetype = vendor_sales
token = 74f7446c-68b6-450c-a807-f355ecd8466d
useACK = 1




curl -k --location 'https://65.0.85.154:8443/services/collector/raw?sourcetype=vendor_sales' --header 'Authorization: Splunk be3cc297-83bf-4554-9280-5ef8fb98fddb' --header 'Content-Type: application/json' --data '[27/Jul/2025:18:43:25] VendorID=1064 Code=B AcctID=6047572642223083'


curl -k -u "x:be3cc297-83bf-4554-9280-5ef8fb98fddb" "https://65.0.85.154:8443/services/collector/event" -d '{"sourcetype": "vendor_sales", "event": "[27/Jul/2025:18:43:25] VendorID=1064 CODE=A AcctID=6047572642223083 password=secret https", "index": "apple"}'



curl -k --location 'https://65.0.85.154:8443/services/collector/raw?channel=00872DC6-AC83-4EDE-8AFE-8413C3825C21&sourcetype=mydata' --header 'Authorization: Splunk be3cc297-83bf-4554-9280-5ef8fb98fddb' --header 'Content-Type: application/json' --data '"hello World"'

curl -k --location 'https://65.0.85.154:8443/services/collector/ack?channel=00872DC6-AC83-4EDE-8AFE-8413C3825C4C' --header 'Authorization: Splunk 74f7446c-68b6-450c-a807-f355ecd8466d' --header 'Content-Type: application/json' --data '{"acks": [0]}'

74f7446c-68b6-450c-a807-f355ecd8466d



curl -k --location 'https://65.0.85.154:8443/services/collector/raw?sourcetype=vendor_sales' --header 'Authorization: Splunk be3cc297-83bf-4554-9280-5ef8fb98fddb' --header 'Content-Type: application/json' --data '[27/Jul/2025:18:43:25] VendorID=1064 Code=B AcctID=6047572642223083'


curl -k -u "x:74f7446c-68b6-450c-a807-f355ecd8466d" "https://65.0.85.154:8443/services/collector/event?channel=00872DC6-AC83-4EDE-8AFE-8413C3825C4C" -d '{"sourcetype": "vendor_sales", "event": "[27/Jul/2025:18:43:25] VendorID=1064 CODE=B AcctID=6047572642223083 password=secret https", "index": "apple"}'

```






























































