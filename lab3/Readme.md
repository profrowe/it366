# Lab 3 - Full ELK Stack on Google Compute (Single Node)

### Steps (Suggest copy+paste line by line to prevent progressing through faults/prompts)
#### Installation
```bash
apt update && apt full-upgrade -y
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
sudo apt update && apt install default-jdk -y
sudo apt-get install elasticsearch=6.6.0 logstash=1:6.6.0-1 kibana=6.6.0 filebeat=6.6.0 metricbeat=6.6.0 auditbeat=6.6.0 -y
sudo systemctl stop elasticsearch
sudo systemctl stop logstash
sudo systemctl stop kibana
sudo systemctl stop filebeat
sudo systemctl stop metricbeat
sudo systemctl stop logstash
```

#### Install Modules/Plugins
```bash
sudo filebeat modules install elasticsearch logstash kibana system
sudo metricbeat modules install elasticsearch logstash kibana
cd /usr/share/elasticsearch
sudo bin/elasticsearch-plugin install ingest-geoip
sudo bin/elasticsearch-plugin install ingest-user-agent
```

#### Config files

config examples as found in confs/ folder.

#### Bring the stack online
```bash
sudo systemctl start elasticsearch
sudo systemctl start logstash
sudo systemctl start kibana
```

#### Test Elasticsearch (should be green)
```bash
curl -X GET "localhost:9200/_cluster/health?pretty=true"
```


#### Install templates & dashboards
```bash
sudo filebeat setup --template -E output.logstash.enabled=false -E output.elasticsearch.enabled=true -E 'output.elasticsearch.hosts=["localhost:9200"]'
sudo metricbeat setup --template -E output.logstash.enabled=false -E output.elasticsearch.enabled=true -E 'output.elasticsearch.hosts=["localhost:9200"]'
sudo auditbeat setup --template -E output.logstash.enabled=false -E output.elasticsearch.enabled=true -E 'output.elasticsearch.hosts=["localhost:9200"]'
sudo filebeat setup --dashboards
sudo metricbeat setup --dashboards
sudo auditbeat setup --dashboards
```

#### Bring beats online
```bash
sudo systemctl start filebeat
sudo systemctl start metricbeat
sudo systemctl start logstash
```
Test for errors with `journalctl -u [servicename] -f` and `tail -f /var/log/[servicename]/[logfile]`

#### Set services to autostart
```bash
sudo systemctl enable elasticsearch
sudo systemctl enable logstash
sudo systemctl enable kibana
sudo systemctl enable filebeat
sudo systemctl enable metricbeat
sudo systemctl enable logstash
```
