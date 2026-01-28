#   SPLUNK SESSIONS

##  Topics to be covered.
-   Components Of Splunk
-   SPlunk License Management
-   Forwarder Management
-   Index Management
-   User Roles, Authentication in Splunk
-   Splunk COnfiguration Files and file precdence
-   Distributed Search
----
-   How to Get Data IN
-   Data Inputs (UF, HEC, SYSLOG, DBConnect)
-   Data Parsing and Transformations
-   Field Extractions and Lookups

-   Splunk Dashboarding...

>   Splunk ONprem --> SPlunk Enterprise --> 
>   Splunk CLoud: -- SAAS



>Splunk Enterprise
-   Heavy Forwarder
-   Indexer
-   Search Head
-   Deployment Server
-   License Master/Manager
-   Cluster Master/ManagerNode
-   Monitoring Console
-   Deployer



configurations (*.conf)

[SOURCETYPE] --> Stanza
key= value

[]

/opt/mahesh/fol1/test1.log

appteam-a


>   ta-appteam-a/default/inputs.conf
>   ta-appteam-a/local/inputs.conf

>   ta-appteam-web/default/inputs.conf
>   ta-appteam-web/local/inputs.conf

>   ta-appteam-cribl/default/inputs.conf
>   ta-appteam-cribl/local/inputs.conf
```
inputs.conf
[monitor:///opt/mahesh/fol1/test1.log]

outputs.conf

```

