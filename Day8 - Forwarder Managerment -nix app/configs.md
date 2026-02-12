outputs.conf

[tcpout]
defaultGroup = splksessions

[tcpout:splksessions]
server = 3.110.133.97:9997
clientCert = $SPLUNK_HOME/etc/auth/server.pem
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem
sslPassword = password
sslVerifyServerCert = false


========================
[monitor:///opt/monitorfiles/]
sourcetype=dell_access_log
index=apple


/opt/splunk/bin/splunk set deploy-poll 3.110.142.190:8089

========================
inputs.conf
[splunktcp-ssl:9997]
connection_host =ip

[SSL]
serverCert = $SPLUNK_HOME/etc/auth/server.pem
sslPassword = password
requrequireClientCert = false
sslVersions = tls1.2
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

========================
server.conf
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

[sslConfig]
sslRootCAPath = $SPLUNK_HOME/etc/auth/cacert.pem

=========================

indexes.conf

[os]
coldPath = $SPLUNK_DB/os/colddb
homePath = $SPLUNK_DB/os/db
thawedPath = $SPLUNK_DB/os/thaweddb


[general]
request_timeout = 300s


[search_client]
polling_interval = 120s

[splunkd_client]
request_timeout = 180s
connection_keep_alive = 10m

[settings_sync]
polling_interval = 10m