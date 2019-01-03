# ELK
Ansible- ELK Stack

## Application Log file with its location

For ElasticSearch- /opt/elasticsearch/log/current
For Kibana- /opt/kibana/log/current

## Configuration

Currently ElasticSearch and Kibana are hosted in WebServer02 instance and the IP address associated with it is exposed to be accesssible from your workstation.

### Configurable variables for ElasticSearch
elasticsearch_host_address: "192.168.10.61"
elasticsearch_host_port: "9200"

### Configurable variables for Kibana
kibana_host_address: "192.168.10.60"
kibana_elasticsearch_url: "http://192.168.10.61:9200"

## Running elk playbook
Linux/Centos - ELK services monitored by runit
```
- hosts: linux
  roles:
    - {role: runit, become: true, become_method: su, become_user: root}
    - {role: elasticsearch, become: true, become_method: su, become_user: root}
    - {role: kibana, become: true, become_method: su, become_user: root}
```

## Command for setting up ELK
If the above Running ELK Playbook command is added to elk.yml file, use the below command for executing playbook
```
ansible-playbook elk.yml --ask-become-pass
```

To run this ELK Playbook against the virtual instances using a different Login
```
ansible-playbook -i inventories/elk/dev/hosts playbooks/elk.yml --ask-become-pass -e "ansible_ssh_pass=PWD"
```

## Checking the Service status
```
sudo sv status elasticsearch
sudo sv status kibana
sudo sv status logstash
sudo sv status elastalert
```

## Curator
This curator does the cleanup of older indices within Elasticsearch and has been scheduled to run through a cron job.

The cron job file is available in the path- /etc/cron.d/elasticsearch-curator
