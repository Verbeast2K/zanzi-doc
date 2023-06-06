# Grafana

## General overview

During this project Grafana was used for the frontend. Grafana is a web-application that is designed to simplify data visualization. It can be used for prototyping and should not be used in a final product. This means that in future iterations of this project Grafana will need to be replaced.

Grafana works with multiple dashboards, each can contain multiple panels. Panels can be graphs, tables, maps, etc ... and there are plenty of plugins that can be used to add even more things. Since Grafana can directly connect to a database, it accepts queries to fill the panels with data, the panels can be modified further by changing the styling settings.

## Usage

As mentioned earlier, Grafana directly connects to databases. These datasources must first be added to your Grafana instance. This can be done using the Grafana API or in the management console (this is the recommended method). For further explanations, see the official Grafana documentation (<https://grafana.com/docs/grafana/latest/datasources/>)

Grafana can import JSON to generate the dashboard/panels (with all including settings/queries) and you can also inspect the JSON of a manually created panel. We used these two facts to create the dashboards. First, we went in the Grafana application to manually create the dashboard with styling and needed query. Second, we inspected the JSON for each panel and copied this into a bigger .JSON file with all other panels (and necessary dashboard information). Not everything that can be found while inspecting the panel's JSON is needed, the main parts that are needed are as follows:

    - title
    - type
    - datasource
    - gridPos: the position of the panel
    - fieldConfig: the styling
    - targets: this contains the queries

The dashboard also needs a title, so the full JSON of a Grafana dashboard would look like this:

```json

{
    "title": "This is an example title",
    "panels": [
        {
            "title": "Example panel",
            "type": "timeseries",
            "datasource": "InfluxDB",
            "gridPos": {
                "h": 8,
                "w": 5,
                "x": 0,
                "y": 0,
            },
            "fieldConfig": {},
            "targets": {},
        },
        {
            "title": "Another example panel",
            "type": "geomap",
            "datasource": "InfluxDB",
            "gridPos": {
                "h": 8,
                "w": 5,
                "x": 8,
                "y": 0,
            },
            "fieldConfig": {},
            "targets": {},
        }
    ]
}

```

Note that this JSON uses a datasource called "InfluxDB", this needs to be created in the management console. The fieldConfig and targets also contain more information, but adding a working fieldConfig and targets would be too much for a simple example.

## Add dashboards to Grafana

Adding a dashboard to Grafana can easily be done via the API. You'll need 2 things for this: the domain name and the API key. You can create an API key under `settings > API keys`. Below you can see the basic structure of the Python scripts to upload the dashboard, run it with `python <file.py>`. It will prompt you for a URL and an API key, then it will try to upload the dashboard & print the response. The scripts we used are located in [this git repository](https://github.com/Jappie3/ZanzibarProjectPython).

```python
import requests
import json

# Get the URL & API key from the user
URL = input(
    "Enter Grafana API URL (e.g. example.com, no protocol or trailing slashes): "
)
GRAFANA_API_URL = "https://" + URL + "/api/dashboards/db"
GRAFANA_API_KEY = input("Enter Grafana API key: ")

# Define  the panels to incluse in the dashboard
PANELS = [
    {
        "title": "Sensor Board Location",
        "type": "geomap",
        "query": "",
        "w": 5,
        "x": 0,
        "y": 0,
        "fieldConfig": {
            "defaults": {
                "custom": {
                    "hideFrom": {
                        "tooltip": False,
                        "viz": False,
                        "legend": False,
                    }
                },
                "mappings": [],
                "thresholds": {
                    "mode": "absolute",
                    "steps": [
                        {"color": "green", "value": None},
                        {"color": "red", "value": 80},
                    ],
                },
                "color": {"mode": "thresholds"},
            },
            "overrides": [],
        },
    },

    # ...

]

# Define the dashboard JSON structure
dashboard = {
    "title": "Sensorboard Dashboard",
    "panels": [],
    "editable": False,
    "hideControls": False,
    "timezone": "browser",
    "time": {"from": "now-6h", "to": "now"},
}

# Add each panel to the dashboard
for panel in PANELS:
    panel_data = {
        "title": panel["title"],
        "type": panel["type"],
        "datasource": "InfluxDB_v2_Flux",
        "gridPos": {"h": 8, "w": panel["w"], "x": panel["x"], "y": panel["y"]},
        "fieldConfig": panel["fieldConfig"],
        "targets": [
            {
                "query": panel["query"],
                "refId": "A",
                "hide": False,
                "type": panel["type"],
            },
        ],
    }

    dashboard["panels"].append(panel_data)

# Define the payload to send to the Grafana API
payload = {
    "dashboard": dashboard,
    "overwrite": True,
}

# Send the request to the Grafana API
response = requests.post(
    GRAFANA_API_URL,
    headers={
        "Authorization": f"Bearer {GRAFANA_API_KEY}",
        "Content-Type": "application/json",
    },
    json=payload,
)

# Print the response from the Grafana API
print(response.text)
```
