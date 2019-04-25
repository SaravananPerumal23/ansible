# Metricbeat
Ansible- Metricbeat

## Configuring Metricbeat

Metricbeat configuration settings are listed below. As per the need, the modules/metricsets can be modified
```
metricbeat_config:
  fields:
    env: "{{ environment_name | default('dev') }}"
  metricbeat.modules:
  - module: system
    metricsets:
      - cpu
      - load
      - core
      - diskio
      - filesystem
      - fsstat
      - memory
      - network
      - process
      - socket
    enabled: true
    period: 60s
    processes: ['.*']
  output.elasticsearch:
    enabled: true
    hosts: ["192.128.20.12:9200"]
    index: "metricbeat-%{+yyyy.MM.dd}"
```

## Setting up Dashboard
To setup metricbeat dashboard in Kibana follow the below steps,
1. Make changes to metricbeat.yml file to include Kibana host information
```
setup.kibana:
  host: "192.128.20.13:5601"
```
2. Restart metricbeat service to take effect of the changes made
3. Open up Powershell terminal as an Administrator mode in case of Windows OS (OR) a Terminal incase of Linux OS and then execute the below command respectively,
For Windows
```
PS > .\metricbeat.exe setup --dashboards
```

For Linux/Centos
```
> .\metricbeat setup --dashboards
```
Note: If you get the error 'Exiting: error loading config file: config file ("metricbeat.yml") must be owned by the beat user (uid=1000) or root', then you need to login as the user (sudo su <USER>) which has permission to metricbeat.yml file and then run the command
4. Verify the Dashboards and visualizations in Kibana. Depending on the system, you can retain the needed Dashboards and delete the other ones if you need to maintain it clean.

For Ex. If your instance is Windows/Linux instance and don't use Docker then the '[Metricbeat Docker] Overview' Dashboard is not essential and can be removed. Also, any associated visualizations can be deleted after verifying that visualization(s) is/are not part of another Dashboard which are essential

## Dashboard visualization issues
When default dashboard gets loaded, we will have few visualizations with errors. This is because of few key fields which differ between visualizations terms and the actual Kibana keywords.

To fix those visualization issues, you need to edit the visualization manually from Kibana and assign the right field to filter

For Ex. '[Metricbeat System] Host overview' Dashboard might have issues loading 'Inbound Traffic [Metricbeat System]'. Open up this visualization and it would point out the field that is configured and which doesn't exist in Kibana as mentioned below,

| Status | Error Description |
|-------|---------|
| The request for this panel failed | Fielddata is disabled on text fields by default. Set fielddata=true on [system.network.name] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead. |

For this issue, you would have to specify the empty field with 'system.network.name.keyword' and that should fix the issue. Apply the changes to verify if that works and then Save it to retain the changes.

Similarly, this has to be done for other visualizations and that should refelct in the Dashboard.


## Dashboard Screenshot

![Alt text](/screenshot/Metricbeat_Dashboard.png?raw=true "Metricbeat Dashboard")
