# Winlogbeat
Ansible- Winlogbeat

## Configuring winlogbeat output

### To File
```
winlogbeat_output:
  file:
    path: "C:/ProgramData/winlogbeat/output/"
    filename: winlogbeat
    #rotate_every_kb: 10000
    #number_of_files: 7
    #permissions: 0600
```

### To Logstash
```
winlogbeat_output:
  logstash:
    hosts: ["192.128.20.13:5044"]
```

## Setting up Dashboard
To setup winlogbeat dashboard in Kibana follow the below steps,
1. Make changes to config .yml file to include Kibana host information
```
setup.kibana:
  host: "192.128.20.13:5601"
```
2. Restart Winlogbeat service to take effect of the changes made
3. Open up Powershell terminal as an Administrator mode and then execute the below command,
```
PS > .\winlogbeat.exe setup --dashboards
```
4. Once you verify the dashboard in Kibana, you can make some changes as per your need


## Dashboard visualization issues
When default dashboard gets loaded, we will have few visualizations with errors. This is because of few key fields which differ between visualizations terms and the actual Kibana keywords.

To fix those visualization issues, you need to edit the visualization manually from Kibana and assign the right field to filter

You could do this by identifying the fields from the visualization json file which is available in 'C:\ProgramData\chocolatey\lib\winlogbeat\tools\kibana\6\dashboard\winlogbeat-overview.json' file
Note: Use Jsonformatter.org website to view this file in JSON format

For each visualization, identify the field that its failing and replace that field with '.keyword' prefix as this is how it is stored in kibana

For Ex. The visualization 'Top Event IDs' json data is provided below,

```
{
    "attributes": {
        "description": "",
        "kibanaSavedObjectMeta": {
            "searchSourceJSON": "{\"filter\": [], \"index\": \"winlogbeat-*\", \"query\": {\"query_string\": {\"analyze_wildcard\": true, \"query\": \"*\"}}}"
        },
        "title": "Top Event IDs",
        "uiStateJSON": "{}",
        "version": 1,
        "visState": "{\"type\": \"table\", \"listeners\": {}, \"params\": {\"perPage\": 10, \"showPartialRows\": false, \"showMeticsAtAllLevels\": false}, \"aggs\": [{\"type\": \"count\", \"params\": {}, \"id\": \"1\", \"schema\": \"metric\"}, {\"type\": \"terms\", \"params\": {\"orderBy\": \"1\", \"field\": \"event_id\", \"order\": \"desc\", \"size\": 5}, \"id\": \"2\", \"schema\": \"bucket\"}]}"
    },
    "id": "Top-Event-IDs",
    "type": "visualization",
    "version": 1
}
```

In this example, 'event_id' is not a actual field in Kibana and cannot use this to show the visualization. So edit the visualization from Kibana and use the field 'event_id.keyword' and apply the changes and if that looks good, you could save this visualization and do the same for other visualizations as well.

## Dashboard Screenshot

![Alt text](/screenshot/Winlogbeat_Dashboard.png?raw=true "Winlogbeat Dashboard")
