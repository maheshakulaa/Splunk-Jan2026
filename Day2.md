##  Splunk Enterprise
-   It is a full software package that collects data from various sources and provides interface to analyse machine generated data.
### Key Features:
>   Collecting text data

>   schemaless

>   Web, CLI, REST API

>   Search, reporting, alerting

>   Anonymizing data

>   scaling to distributed and clustered deployments

>   HA & DR

##  Splunk Components
-   Heavy Forwarder
-   Indexer
-   Search Head
>   Management components
-   Deployment Server
-   License Master/Manager
-   Cluster Master/ManagerNode
-   Monitoring Console
-   Deployer

```
Splunk Universal Forwarder ⟶ Light weight  component 
Splunk Enterprise ⟶ full package



Splunk Enterprise 
Heavy Forwarder HF
Indexer
Searchead
Deployment server DS
Deployer
Monitoring console MC
Cluster Master/Master Node CM
License Master LM

Collection tier/Data onboarding
Push
Splunk agent/Splunk UF
HTTP Event Collector HEC
SYSLOG
Pull
API based
Splunk TAs ⟶ Technical Addons
Custom TAs(customer/user managed)
```
##  Splunk Architectures
![alt text](image.png)
##  Splunk Universal Forwarder
-   It is a light weight component provided by splunk typically  installed on source system where data is being generated.
-   It has inouts configuations inputs.conf with a list of file paths along with metadata fields - host, source, sourcetype.
-   It uses fishbucket process to forward data exactly once.

----
##  Splunk Enterprise Installation
```
Splunk ( links)

https://www.splunk.com/
https://www.aplura.com/splunk-best-practices/

https://www.splunk.com/en_us/blog/tips-and-tricks/splunk-validated-architectures.html
https://conf.splunk.com/watch/conf-online.html#/


UF:
wget -O splunkforwarder-10.0.0-e8eb0c4654f8-linux-amd64.tgz "https://download.splunk.com/products/universalforwarder/releases/10.0.0/linux/splunkforwarder-10.0.0-e8eb0c4654f8-linux-amd64.tgz"

tar -xzvf splunkforwarder-10.0.0-e8eb0c4654f8-linux-amd64.tgz
/opt/splunkforwarder/ ⟶ $SPLUNK_HOME


Enterprise:
wget -O splunk-10.0.0-e8eb0c4654f8-linux-amd64.tgz "https://download.splunk.com/products/splunk/releases/10.0.0/linux/splunk-10.0.0-e8eb0c4654f8-linux-amd64.tgz"

tar -xzvf splunk-10.0.0-e8eb0c4654f8-linux-amd64.tgz

/opt/splunk/ ⟶ $SPLUNK_HOME
/opt/splunk/var/lib/splunk ⟶ $SPLUNK_DB



Splunk Commands:

$SPLUNK_HOME/bin/splunk start --accept-license ⟶ This is for first time starting splunk process.
$SPLUNK_HOME/bin/splunk start
$SPLUNK_HOME/bin/splunk stop
$SPLUNK_HOME/bin/splunk restart
$SPLUNK_HOME/bin/splunk status
$SPLUNK_HOME/bin/splunk show web-port ( to get web port on which splunk is running)








```
##  Splunk UF installation
