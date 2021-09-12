
# DEB 다운로드 및 설치
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.14.1-amd64.deb
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.14.1-amd64.deb
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.14.1-amd64.deb
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.14.1-amd64.deb

sudo dpkg -i elasticsearch-7.14.1-amd64.deb
sudo dpkg -i kibana-7.14.1-amd64.deb
sudo dpkg -i logstash-7.14.1-amd64.deb
sudo dpkg -i filebeat-7.14.1-amd64.deb


sudo service elasticsearch start
sudo netstat -antp|grep 9200
sudo service kibana start
sudo netstat -antp|grep 5601

# 직접 설치
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.14.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.14.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.14.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.14.1-linux-x86_64.tar.gz

# OS환경설정
sudo sysctl -w vm.max_map_count=262144
/etc/sysctl.conf 에 아래내용 추가
vm.max_map_count=262144

/etc/security/limits.conf
*       soft    nproc   65536
*       hard    nproc   65536
*       soft    nofile  65536
*       hard    nofile  65536

# vi elasticsearch.yml
cluster.name: my-elk
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
transport.tcp.port: 9300
discovery.seed_hosts: ["0.0.0.0"]
cluster.initial_master_nodes: ["node-1"]


http://13.124.37.156:9200/_cat/nodes?v
http://13.124.37.156:9200/_cat/indices?v
http://13.124.37.156:9200/_cat/health?v
