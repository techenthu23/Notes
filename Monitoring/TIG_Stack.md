--- 
layout: default 
title:  "TIG Stack (Telegraf, InfluxDB, and Grafana)"
---  

# Telegraf, InfluxDB, and Grafana

From all the existing modern monitoring tools, the TIG (Telegraf, InfluxDB and Grafana) stack is probably one of the most popular ones.

This stack can be used to monitor a wide panel of different datasources: from operating systems (such as Linux or Windows performance metrics), to databases (such as MongoDB or MySQL), the possibilities are endless.

**InfluxDB** is an open-source time series database written in Go. Optimized for fast, high-availability storage and used as a data store for any use case involving large amounts of time-stamped data, including DevOps monitoring, log data, application metrics, IoT sensor data, and real-time analytics.

**Telegraf** is an open source server agent for collecting, processing, aggregating metrics from your stacks, sensors and systems and write them into a wide array of outputs. It also has output plugins to send metrics to a variety of other datastores, services, and message queues, including InfluxDB, Graphite, OpenTSDB, Datadog, Librato, Kafka, MQTT, NSQ, and many others.

It is written in Go, which means that it is a compiled and standalone binary that can be executed on any system with no need for external dependencies, no npm, pip, gem, or other package management tools required.

**Grafana** is an open source data visualization and monitoring suite. It offers support for Graphite, Elasticsearch, Prometheus, influxdb, and many more databases. The tool provides a beautiful dashboard and metric analytics, with the ability to manage and create your own dashboard for your apps or infrastructure performance monitoring.

A Modern Monitoring Architecture

```
          Send data every in 10 sec via HTTPS             Queries InfluxDB periodically via HTTPS
telegraf -------------------------------------> influxdb <---------------------------------------- Grafana Instance
    |
    |
    | Gathers internal metrics (CPU, Mem,..)
    V
Linux System                                                                                        

```

1) Install InfluxDB

In this first step, we will install the time series database influxdb on the system. We will install both 'influxdb' and the 'telegraf' from the same 'influxdata' Repository, both software were created by the same organization.

Configure the yum repository

```
cat <<EOF | tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository - RHEL \$releasever
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```

Update cache to confirm that the repository is working fine:

```
yum makecache fast  # Only on RHEL7
dnf makecache       # Only on RHEL8
```

install influxDB

```
dnf -y install influxdb
```

2) Start and enable the service

```
systemctl start influxdb && systemctl enable influxdb
```

3) Configure InfluxDB firewall

By default, InfluxDB uses the following network ports:

TCP port 8086 is used for client-server communication over InfluxDB’s HTTP API
TCP port 8088 is used for the RPC service for backup and restore.

To open it on the firewall, use the command:

```
firewall-cmd --add-port=8086/tcp --permanent
firewall-cmd --reload
```

> Port mappings can be modified by changing the file /etc/influxdb/influxdb.conf.

Since all is configured now, we can restart the service now.

```
systemctl restart influxdb
```

4) InfluxDB http Authentication (Optional)

If you need http authentication, modify influxdb http section to contain the following.

```
$vim /etc/influxdb/influxdb.conf
[http]
auth-enabled = true
```

Then create a user with an authentication password:

```
curl -XPOST "http://localhost:8086/query" --data-urlencode \
"q=CREATE USER username_here WITH PASSWORD 'password_here' WITH ALL PRIVILEGES"
```

Now whenever you need to run any influxdb commands on the terminal, you need to specify username using -username and password using -password options.

```
influx -username 'username_here' -password 'password_here'
```

For curl, use -u to specify username and password separated by a colon.

```
curl -G <http://localhost:8086/query> -u username:password --data-urlencode "q=SHOW DATABASES"
```

By default, influxdb service is listening on all interfaces on port 8086.

```
$ sudo ss -tunelp | grep 8086
 tcp   LISTEN  0  128     *:8086*:* users:(("influxd",pid=2072,fd=5)) uid:985 ino:37787 sk:6 v6only:0 <->
```

You now have InfluxDB installed

5) You can view the current configuration with:

```
influxd config
```

The configuration file can be found in: /etc/influxdb/influxdb.conf
The influxd binary is located at /usr/bin/influxd

6) Configure your InfluxDB instance

To ensure HTTP endpoint is correctly enabled, Head over to the [http], and verify the enabled parameter.