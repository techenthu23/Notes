--- 
layout: default 
title:  "Grafana"
---  

- [What is Grafana?](#what-is-grafana)
- [Grafana Features](#grafana-features)
- [Installing Grafana on Linux](#installing-grafana-on-linux)
- [Monitor Linux Server Performance with Prometheus and Grafana](#monitor-linux-server-performance-with-prometheus-and-grafana)

# What is Grafana?

Grafana is an open source monitoring tool with Dashboards and Visualizations. Grafana is an open-source analytics and interactive visualization web application. It allows you to ingest data from a huge number of data sources, query this data and display it on beautiful customizable charts for easy analysis.

It can integrated with several other services such as Prometheus , Elasticsearch , Cloudwatch , Loki , InfluxDB , Graphite etc.

Grafana also helps us to alert to several channels such as Email , Slack , Opsgenie , Webhook , Telegram etc.

# Grafana Features

1) Visualization
  Grafana possesses a huge variety of visualization options to help you view and understand your data easily. These options are split into “panels” which are then used to build the Grafana dashboard.

  A panel is the most granular visualization building block in Grafana, and is used to display data that has been queried from the data source attributed to that panel. For easier understanding, think of a panel as a space on the dashboard that houses a specific type of visual portrait of information.

2) Alerting
  When monitoring applications, it is essential to be made aware the second something goes wrong, or is abnormal. This is vital to keeping your systems healthy and reducing downtime. Grafana has built-in support for a huge number of notification channels, be it email, Slack, PagerDuty, etc., whichever best suits you.

3) Annotations
  Grafana allows you to annotate, or in simple terms, leave notes directly on graphs. This simple but powerful feature provides a way to seamlessly mark important points on your graph. This serves as a reminder for further action in the future, to provide context to an onboarding team member, or to simply mark a special event on your graph.

  Think of it as writing a sticky note and placing it directly on your graph

4) Open Source
  Grafana is completely open source and backed by an active vibrant community. This presents some huge benefits to its users such as the flexibility to develop and publish their own plugins or use plugins developed by other people. These plugins are usually easy to install by pretty much downloading the source code and running it manually.

  However, its open source nature does pave the way for some drawbacks. For example, you would have to manually maintain your Grafana instance yourself, manually perform updates and so on.

# Installing Grafana on Linux

1) Add Grafana yum repository

  ```bash
  cat <<EOF | tee /etc/yum.repos.d/grafana.repo
  [grafana]
  name=grafana
  baseurl=https://packages.grafana.com/oss/rpm
  repo_gpgcheck=1
  enabled=1
  gpgcheck=1
  gpgkey=https://packages.grafana.com/gpg.key
  sslverify=1
  sslcacert=/etc/pki/tls/certs/ca-bundle.crt
  EOF
  ```

  You can optionally update you cache index for available packages:

  ```bash
  dnf makecache
  ```

2) Install Grafana

  ```bash
  dnf -y install grafana
  ```

3) Start Grafana Service

  ```bash
  systemctl enable --now grafana-server.service
  ```

  By default,

  - port used is 3000. If you have another process using this port, you’ll need to set custom port in Grafana configuration file /etc/grafana/grafana.ini.
  - Grafana will write logs to /var/log/grafana directory and its SQLite database is located under /var/lib/grafana/grafana.db

4) Open firewall port for Grafana

  If you have a running firewalld service, allow port 3000for access to the dashboard from the network:

  ```bash
  firewall-cmd --add-port=3000/tcp --permanent
  firewall-cmd --reload
  ```

5) Access Grafana Dashboard
   Grafana web dashboard is accessible on <http://[Server> IP|Hostname]:3000  and the  default logins are admin/admin

# Monitor Linux Server Performance with Prometheus and Grafana

Prometheus is an open source monitoring solution that stores all its data in a time series database. Prometheus has a multi-dimensional data-model and a powerful query language that is used to generate reports of the resources being monitored.

Prometheus node exporter exports hardware and OS metrics exposed by *NIX kernels for consumption by Prometheus. This exporter is written in Go with pluggable metric collectors.

1) Add Prometheus system user
   Add a user account to run nod exporter service. It is safe since it doesn’t have access to the interactive shell and home directory.

  ```bash
  groupadd --system prometheus
  useradd -s /sbin/nologin --system -g prometheus prometheus
  ```

2) Download and Install Prometheus Node Exporter

   ```bash
   curl -s https://api.github.com/repos/prometheus/node_exporter/releases/latest | grep browser_download_url | grep linux-amd64 |  cut -d '"' -f 4 | wget -qi -
   tar xvf node_exporter-*linux-amd64.tar.gz
   # Move the downloaded file to the /usr/local/bin directory
   cd node_exporter*/
   mv node_exporter /usr/local/bin/

   # Confirm Installed version
   node_exporter  --version
   ```

3) Configure Prometheus Node Exporter systemd / Init scri
   Collectors are enabled by providing a --collector.<name> flag. Collectors that are enabled by default can be disabled by providing a --no-collector.<name>flag.0

  `vim /etc/systemd/system/node_exporter.service`

  ```bash
  [Unit]
  Description=Prometheus
  Documentation=https://github.com/prometheus/node_exporter
  Wants=network-online.target
  After=network-online.target

  [Service]
  Type=simple
  User=prometheus
  Group=prometheus
  ExecReload=/bin/kill -HUP $MAINPID
  ExecStart=/usr/local/bin/node_exporter \
      --collector.cpu \
      --collector.diskstats \
      --collector.filesystem \
      --collector.loadavg \
      --collector.meminfo \
      --collector.filefd \
      --collector.netdev \
      --collector.stat \
      --collector.netstat \
      --collector.systemd \
      --collector.uname \
      --collector.vmstat \
      --collector.time \
      --collector.mdadm \
      --collector.zfs \
      --collector.tcpstat \
      --collector.bonding \
      --collector.hwmon \
      --collector.arp \
      --web.listen-address=:9100 \
      --web.telemetry-path="/metrics"
  ```

  Start the service and enable it to start on boot

  ```bash
  systemctl start node_exporter
  systemctl enable node_exporter
  ```

4) Configure firewall

  If you have an active firewall on your server, e.g firewalld, ufw, open port 9100

  ```bash
  ufw allow 9100
  ```

  For CentOS 7 system, use firewalld:

  ```bash
  firewall-cmd --add-port=9100/tcp --permanent
  firewall-cmd --reload
  ```

  For Init Linux system like CentOS 6, you can use daemonize to start the service in the background.

  Install daemonize:

  ```bash
  yum install daemonize
  apt-get install daemonize
  ```

  Once installed, create node_exporter init script:

  ```bash
  vim /etc/init.d/node_exporter
  ```

  Add below script:

  ```bash
  #!/bin/bash
  # Author: Josphat Mutai, josphatkmutai@gmail.com , https://github.com/jmutai
  # node_exporter     This shell script takes care of starting and stopping Prometheus apache exporter
  #
  # chkconfig: 2345 80 80
  # description: Prometheus apache exporter  start script
  # processname: node_exporter
  # pidfile: /var/run/node_exporter.pid

  # Source function library.
  . /etc/rc.d/init.d/functions

  RETVAL=0
  PROGNAME=node_exporter
  PROG=/usr/local/bin/${PROGNAME}
  RUNAS=prometheus
  LOCKFILE=/var/lock/subsys/${PROGNAME}
  PIDFILE=/var/run/${PROGNAME}.pid
  LOGFILE=/var/log/${PROGNAME}.log
  DAEMON_SYSCONFIG=/etc/sysconfig/${PROGNAME}

  # GO CPU core Limit

  #GOMAXPROCS=$(grep -c ^processor /proc/cpuinfo)
  GOMAXPROCS=1

  # Source config

  . ${DAEMON_SYSCONFIG}

  start() {
      if [[ -f $PIDFILE ]] > /dev/null; then
          echo "node_exporter  is already running"
          exit 0
      fi

      echo -n "Starting node_exporter  service…"
      daemonize -u ${USER} -p ${PIDFILE} -l ${LOCKFILE} -a -e ${LOGFILE} -o ${LOGFILE} ${PROG} ${ARGS}
      RETVAL=$?
      echo ""
      return $RETVAL
  }

  stop() {
      if [ ! -f "$PIDFILE" ] || ! kill -0 $(cat "$PIDFILE"); then
          echo "Service not running"
          return 1
      fi
      echo 'Stopping service…'
      #kill -15 $(cat "$PIDFILE") && rm -f "$PIDFILE"
      killproc -p ${PIDFILE} -d 10 ${PROG}
      RETVAL=$?
      echo
      [ $RETVAL = 0 ] && rm -f ${LOCKFILE} ${PIDFILE}
      return $RETVAL
  }

  status() {
      if [ -f "$PIDFILE" ] || kill -0 $(cat "$PIDFILE"); then
        echo "apache exporter  service running..."
        echo "Service PID: `cat $PIDFILE`"
      else
        echo "Service not running"
      fi
      RETVAL=$?
      return $RETVAL
  }

  # Call function
  case "$1" in
      start)
          start
          ;;
      stop)
          stop
          ;;
      restart)
          stop
          start
          ;;
      status)
          status
          ;;
      *)
          echo "Usage: $0 {start|stop|restart}"
          exit 2
  esac
  ```

  Create Arguments configuration file:

  ```bash
  vim /etc/sysconfig/node_exporter
  ```

  Add:

  ```bash
  "--collector.cpu \
  --collector.diskstats \
  --collector.filesystem \
  --collector.loadavg \
  --collector.meminfo \
  --collector.filefd \
  --collector.netdev \
  --collector.stat \
  --collector.netstat \
  --collector.systemd \
  --collector.uname \
  --collector.vmstat \
  --collector.time \
  --collector.mdadm \
  --collector.xfs \
  --collector.zfs \
  --collector.tcpstat \
  --collector.bonding \
  --collector.hwmon \
  --collector.arp \
  --web.listen-address=:9100
  ```

  Test the script:

  ```bash
  # /etc/init.d/node_exporter
  Usage: /etc/init.d/node_exporter {start|stop|restart}
  ```

5) Start the Prometheus node exporter service
  For systemd, start using:

  ```bash
  systemctl start node_exporter
  systemctl enable node_exporter
  ```

  For Init system use:

  ```bash
  /etc/init.d/node_exporter start
  chkconfig node_exporter on
  ```

  You can verify using:

  ```bash
  ss -tunelp | grep 9100
  tcp    LISTEN     0      128      :::9100                 :::*                   users:(("node_exporter",pid=16105,fd=3)) uid:997 ino:193468 sk:ffff8a0a76f52a80 v6only:0 <->
  ```

6) Add exporter job to Prometheus
  The second last step is to add a job to the Prometheus server for scraping metrics.

  Edit /etc/prometheus/prometheus.yml

  ```bash
  # Linux Servers
    - job_name: apache-linux-server1
      static_configs:
        - targets: ['10.1.10.20:9100']
          labels:
            alias: server1

    - job_name: apache-linux-server2
      static_configs:
        - targets: ['10.1.10.21:9100']
          labels:
            alias: server2
  ```

  Restart prometheus service for scraping to start and test access to port 9100 from Prometheus server

  ```bash
  systemctl restart prometheus
  nc -vz 10.1.10.20 9100
  ```

7) Integrating Prometheus with Grafana
   
    To check the node exporters metrics which is alread stored in the prometheus server , We need to integrate prometheus with grafana.

    1) Login to Grafana
    2) To integrate , Hover to Settings icon and choose Datasources
    3) Under Configuration , Click Add Data source and then choose Prometheus
    4) Here it will asked to provide few information such as Prometheus URL
    5) Provide a name for the datasource , and then provide the ip address and the port in which Prometheus is Running.
      Note: Grafana should be able to access port 9090 and it should be whitelisted in the Firewall.
    6) Now click Save and Test , It should response as Data source is working

- **Setup Alerting in Grafana**

  For the Grafana to alert to a specific channel or medium, We need to add / configure the same.

  1) Choose Alerting and then click Notification channels
  2) Click Add channel , From the lists of available notification channels , Choose the one where you will be active.
  3) Provide a name for the Notification Channel and then choose Type.
  4) Once done , Click Save.

- **Setting Up Grafana Dashboards**
  
  We can setup dashboard by ourselves from the scratch or we can import the existing dashboard from Grafana or from a collection of community shared dashboards. To setup a dashboard for the Node Exporter metrics.

  1) In the left side , Hover to + icon and choose Import
  2) It will ask us the id of the dashboard. The id to import the Node exporter graph is 9096. Provide the id and then click Load
  3) Give a name for the dashboard and then choose the data source as Prometheus and then click Import.

  It will collect all the metrics from the Prometheus data source and plot the graph as per the metrics such as CPU usage , Disk usage , Memory usage etc.

  Make sure the dashboard id’s are chose as per the version of the Node exporter installed , Else It won’t show up any data in the graphs.

- **Setup Centralized Monitoring and Alerting Dashboard**
  
  We can setup a dashboard where we can check the metrics of the servers and at the same time we can check the alert status.

  The id to import the dashboard is 5984

  Once the dashboard is imported , You can see Disk usage , Memory and CPU usages.

  To setup alerting for this, Lets say If the CPU usage is above 80% (for example) alert it to the Notification channel we have configured.

  1) From the drop down of the CPU usage graph , Choose Edit
  2) From the left navigation pane , Choose the Alerting icon
  3) Provide a name for the alert , and configure how often the data should be evaluated & based on that If the usage if above the threshold we set, It will alert us.
  4) Scroll down and under Notifications , Click + and select the Notification channel
  5) And provide a message for the alert ,For better Understanding.
  6) and then click the Save icon on top right corner of the dashboard.
  7) Repeat the same procedures for the other metrics / graphs.

  We can also configure multiple conditions while configuring alerting by various levels such as Warning , High and Critical.
