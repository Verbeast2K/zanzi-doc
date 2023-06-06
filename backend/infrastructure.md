# Backend infrastructure

- The backend for the FLWSB project consists of the following applications:
	- Influxdb: open source time series database
		- Used to store data gathered by the sensors & weather station
	- Mosquitto: open source server implementation of the MQTT protocol
		- Receives messages from the gateway
		- MQTT server for weather station data (weather station doesn't work with LoRaWAN)
	- NodeRuby: custom API written in JavaScript
		- Formats the data received from the sensor boards (from The Things Network via the MQTT protocol) & from Mosquitto (listens for weather station data via MQTT)
		- Sends the data to InfluxDB
	- Nginx: acts as a reverse proxy for the website and the API
	- Grafana: open source web application for analytics and interactive visualization
		- Visualize weather data on a website
