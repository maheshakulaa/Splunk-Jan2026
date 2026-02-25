
##  Scenario/usecase

Onboard vendor sales logs to splunk and route "Code=F" event logs to vendor index, drop events for "Code=Z" and all other to main index, extract AcctID at index time, mask any sensitive data like passwords before indexing in splunk.

Allow only vendor team to access vendor index


create a dashbaord 
-   to check how many distinct account IDs logged in
-   stats - count per code and represent in a pie chart
-   create a trend chart for each code

----
----
### Step1:
```
Prerequisites & best practices:

    check hardware reference doc for planning capacity
    check connectivity between servers on required ports as mentioned in arch diagram
    Install Splunk by accepting license
    enable bootstart
    Disable web on hf and indexers
    enable https on sh & deploymentserver(DS)
```
### Step2:
```
on all Non DS instances: create deploymentclient.conf

$SPLUNK_HOME/bin/splunk set deploy-poll <deploymentserver>:8089

```

### Step3:
```
create below apps for indexer, uf, sh and hf
/deployment-apps
    ta_uf_base/local/{inputs.conf,outputs.conf}
    ta_hf_base/local/{props.conf,transforms.conf,outputs.conf,inputs.conf,server.conf,web.conf}
    ta_idx_base/local/{inputs.conf,indexes.conf,server.conf,web.conf}
    ta_sh_base/local/{outputs.conf,web.conf}

under etc/apps/ create a app to index ds data to show agent info in forwarder management and forward to data.
    ta_ds_app/local/{outputs.conf,web.conf}

```
-   Configs for above apps
```
#####################    ta_hf_base     #######################         

props.conf
[vendor_sales]
SHOULD_LINEMERGE=false
LINE_BREAKER=([\r\n]+)(\[\d{1,2}\/\w{3}\/\d{4}:\d{1,2}:\d{1,2}\:\d{1,2}\])
NO_BINARY_CHECK=true
CHARSET=UTF-8
TIME_FORMAT=%d/%b/%Y:%H:%M:%S
TIME_PREFIX=^\[
TRANSFORMS-route_to_idx = route_to_vendorindex,extractaccount
TRANSFORMS-routetonull = routetoNullqueeue
SEDCMD-maskpassword = s/(?i)((?:password|pwd|passwd)(?:[\s\=\:]+))(\S+)/\1XXXXXXX/g

========================
transforms.conf
[routetoNullqueeue]
REGEX = Code\=Z
DEST_KEY = queue
FORMAT = nullQueue

[extractaccount]
REGEX = (AcctID)\=(\d+)
FORMAT = $1::$12
WRITE_META = true

[route_to_vendorindex]
REGEX=Code\=F
DEST_KEY= _MetaData:Index
FORMAT=vendor
=======================================
outputs.conf
[tcpout]
defaultGroup = myindexergroup

[tcpout:myindexergroup]
server = 3.110.133.97:9997
clientCert = $SPLUNK_HOME/etc/auth/server.pem
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
sslPassword = password
sslVerifyServerCert = false

=================================
inputs.conf
[splunktcp-ssl:9997]
connection_host =ip

[SSL]
serverCert = $SPLUNK_HOME/etc/auth/server.pem
sslPassword = password
requrequireClientCert = false
sslVersions = tls1.2
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

==================================
server.conf
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

[sslConfig]
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
=================================
web.conf
[settings]
startwebserver=0
```

```
#####################    ta_idx_base     #######################  
server.conf

sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

[sslConfig]
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

=====================
inputs.conf
[splunktcp-ssl:9997]
connection_host =ip

[SSL]
serverCert = $SPLUNK_HOME/etc/auth/server.pem
sslPassword = password
requrequireClientCert = false
sslVersions = tls1.2
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

=====================
indexes.conf
[vendor]
coldPath = $SPLUNK_DB/vendor/colddb
homePath = $SPLUNK_DB/os/db
thawedPath = $SPLUNK_DB/vendor/thaweddb
=================================
web.conf
[settings]
startwebserver=0
```

```
#####################    ta_sh_base     #######################  
outputs.conf
[tcpout]
defaultGroup = myhf_group

[tcpout:myhf_group]
server = 3.110.133.97:9997
clientCert = $SPLUNK_HOME/etc/auth/server.pem
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
sslPassword = password
sslVerifyServerCert = false

======================
web.conf
[settings]
enableSplunkWebSSL = true
```

```
#####################    ta_uf_base     #######################  
outputs.conf
[tcpout]
defaultGroup = myhf_group

[tcpout:myhf_group]
server = 3.110.133.97:9997
clientCert = $SPLUNK_HOME/etc/auth/server.pem
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
sslPassword = password
sslVerifyServerCert = false

==========================
inputs.conf
[monitor:///opt/monitor/*.log]
sourcetype=vendor_sales
index=main
```

```
#####################    ta_ds_app     #######################

outputs.conf
[indexAndForward]
index = true
selectiveIndexing = true

[tcpout]
defaultGroup = myhf_group

[tcpout:myhf_group]
server = 3.110.133.97:9997
clientCert = $SPLUNK_HOME/etc/auth/server.pem
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
sslPassword = password
sslVerifyServerCert = false
======
web.conf
[settings]
enableSplunkWebSSL = true

```

### Step4:
create below serverclasses in DS and add respective clients and enable splunkd restart 
-   uf_base
-   idx_base
-   sh_base
-   hf_base