##  Splunk Addons & Apps

>   addons (inputs and setup config)  and Apps (KOs)
----
Addons types
-   TA - Technology Addon
-   SA - Supporting addon
-   DA - Domain Addon


Addon:
-   Data collection (inputs/scripts...)
-   Field extractions
-   No UI (very minimal UI)
-   No Dashboards, reports, alerts (KOs)
-   on indexers, Hfs, Ufs
-   SH only for field extractions

App:
-   All KOs (Knowledge Objects)
-   It has UI
-   only on Search Head




##  Splunk DBConnect
Installation steps:
-   Download/install DB connect from  splunk base on HF/indexer

Prerequisites:
-   jre 17/21

1.  Install Java 21 on splunk instance
2.  Check java --version
3.  Get the jre location 
4.  configure in splunk db connect app
5. install mysql addon (Splunk DBX Add-on for MySQL JDBC) MySQL
```
ON DB VM:

sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 
sudo dnf install mysql80-community-release-el9-1.noarch.rpm -y
sudo yum install mysql-server -y --nogpgcheck
mysql --version

sudo systemctl start mysqld
sudo systemctl enable mysqld
sudo systemctl status mysqld

get password:
cat /var/log/mysqld.log

j4u:eOwUPexq

sudo mysql_secure_installation



mysql -u root -p

----------------------------
SHOW DATABASES;
CREATE DATABASE splunk;
use splunk;
SELECT DATABASE();

create table splunkusers (id INT AUTO_INCREMENT PRIMARY KEY,
name varchar(30),
company varchar(30));

insert into splunkusers (name, company) values
('India','ibm'),
('America','xyz'),
('Russia','abc');


create user 'splunk_user'@'%' identified by 'Splunk@1234';
grant all privileges on splunk.* to 'splunk_user'@'%';
flush privileges;


-----------------------------------------------
-----------------------------------------------
-----------------------------------------------

ON Indexer:
sudo yum install java-21-amazon-corretto-devel
jre path:

/usr/lib/jvm/jre-21-openjdk

-----------------------------------------------

```


-   https://splunkbase.splunk.com/app/2686

-   https://docs.splunk.com/Documentation/DBX/3.8.0/DeployDBX/AboutSplunkDBConnect
-   https://docs.splunk.com/Documentation/DBX/3.8.0/DeployDBX/Installdatabasedrivers

