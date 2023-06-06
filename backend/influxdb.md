# Influxdb

## General overview

For this project we decided to use a time-based database called "InfluxDB". It is a non-relational database that uses a timestamp as a unique identifier. It is ideal for IoT-projects that require a lot of measurements, there are also a wide variety of client-libraries that can be used to integrate InfluxDB into your code (Python libraries, Java dependencies, npm packages, etc...). Since most of the programs that connect to the database are written in JavaScript, the npm packages were used.

InfluxDB works with buckets, measurements, tags, fields and timestamp: - Buckets can be considered the database where you store your measurements. - Measurements are like tables. - Tags are extra pieces of information (stored as strings) that can be used to execute specific queries. Tags are extremely usefull when the data can come from multiple sources (2 sensors, thus they need an ID). - Fields are the actual value that is being send (for example: a temperature with value 30) - Timestamps can be used to specify a specific time. If no timestamp is given, InfluxDB will automatically assign the current server time.

## Usage

There are multiple ways to insert data into InfluxDB, the most basic one is manually inserting line protocol (<https://docs.influxdata.com/influxdb/v1.8/write_protocols/line_protocol_tutorial/>), but using the InfluxDB API is more efficient.

```js
// Import necessary package
const { InfluxDB, Point } = require("@influxdata/influxdb-client");

// Initial set-up
const influxDB = new InfluxDB({
  url: INSERT URL,
  token: INSERT TOKEN,
});
const writeApi = influxDB.getWriteApi(
  INSERT ORG,
  INSERT BUCKET
);

// Creation of a point
const point = new Point("example_measurement")
    .tag("exampleId",1)
    .floatField("temp",25.0)
    .intField("humidity",50);

// Writing said point to the database
writeApi.writePoint(point);
```

In the code block above is the most basic way to write data to InfluxDB (without manually inserting the data). There are plenty of sources of other libraries (Python, Java, .NET, etc...). This method is also used in the project (with added formatting and error handling). Because of the more flexible nature of InfluxDb, the data that is being inserted does not always need to be the same format (some fields may be missing for example). This method is used when handling the errors in the sensormeasurements.

```js
const { InfluxDB, Point } = require("@influxdata/influxdb-client");

const influxDB = new InfluxDB({
  url: process.env.INFLUXDB_URL,
  token: process.env.INFLUXDB_TOKEN,
});
const writeApi = influxDB.getWriteApi(
  process.env.INFLUXDB_ORG,
  process.env.INFLUXDB_BUCKET
);

let point = new Point("sensor_data").tag(
      "id",
      payloadmsg.end_device_ids.device_id
    );
const payload = payloadmsg.uplink_message.decoded_payload;
  if (payload.humidity_BME <= 100 && payload.humidity_BME >= 0) {
    point.floatField("humidity_BME", payload.humidity_BME);
  }
  if (payload.pressure_BME <= 1100 && payload.pressure_BME >= 300) {
    point.floatField("pressure_BME", payload.pressure_BME);
  }

writeApi.writePoint(point);
```

The code above for example would add the humidity/pressure value to the point if the value is between the given values. If one of the values are not between the values then it would not be added to the point.

## Viewing the data

To view all the data, you can use the data explorer in InfluxDB GUI (default port 8086). The data explorer can also be used to generate Flux queries.
