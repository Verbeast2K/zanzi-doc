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