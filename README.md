## Analyze NewYork City Taxi Data Using ELK Stack
This is a small project to perform ingesting and analysing data from csv using ELK Stack. 
The project uses taxi trips data in NYC in 2014. 


##### Version
Example has been tested in following versions:
- Elasticsearch 6.0
- Filebeat 6.0
- Kibana 6.0

### Installation & Setup
* Follow the [Installation & Setup Guide](https://github.com/elastic/examples/blob/master/Installation%20and%20Setup.md) to install and test the ELK stack (*you can skip this step if you already have a working installation of the ELK stack*)

* Run Elasticsearch & Kibana
  ```shell
  <path_to_elasticsearch_root_dir>/bin/elasticsearch
  <path_to_kibana_root_dir>/bin/kibana
  ```

* Check that Elasticsearch and Kibana are up and running.
  - Open `localhost:9200` in web browser -- should return status code 200
  - Open `localhost:5601` in web browser -- should display Kibana UI.

  **Note:** By default, Elasticsearch runs on port 9200, and Kibana run on ports 5601. If you changed the default ports, change   the above calls to use appropriate ports.

* Download and install Filebeat as described [here](https://www.elastic.co/guide/en/beats/filebeat/5.4/filebeat-installation.html). **Do not start Filebeat**

### Download Data & Example Files

* **Download Data Files from link:**
    ```shell
   https://www.kaggle.com/kentonnlp/2014-new-york-city-taxi-trips/downloads/2014-new-york-city-taxi-trips.zip/ 
    ```
    ### Run Example

##### 1. Ingest data into Elasticsearch using Logstash

* Install the nyc_taxi_trips ingest pipeline i.e.

    ```shell
    curl -XPUT -H 'Content-Type: application/json' 'localhost:9200/_ingest/pipeline/nyc_taxi_trips' -d @nyc_taxi_trips_pipeline.json
    ```

* Install the nyc_taxi_trips index template i.e.

    ```shell
    curl -XPUT -H 'Content-Type: application/json' 'localhost:9200/_template/nyc_taxi_trips' -d @nyc_taxi_trips_template.json
    ```

*  Modify the `nyc_taxi_trips_filebeat.yml` file as follows:

    * The parameter `hosts: ["localhost:9200"]` in case your are not running Elasticsearch node on your local host
    * The path to the csv file download above:
    
        ```shell
          paths:
            - ./path/to/file/nyc_taxi_data_2014.csv
        ```

* Move the file `nyc_taxi_trips_filebeat.yml` to the Filebeat installation directory i.e.
    
     ```shell
    mv nyc_taxi_trips_filebeat.yml <filebeat_installation_dir>/nyc_taxi_trips_filebeat.yml
    ```
    In case, "Permission Denied" error occurs, use sudo to run the above command.   
* Start Filebeat to begin ingesting data to Elasticsearch

    ```shell
    cd <filebeat_installation_dir>
    ./filebeat -e -c nyc_taxi_trips_filebeat.yml
    ```
    
    ##### 2. Visualize data in Kibana

* Access Kibana by going to `http://localhost:5601` in a web browser
* Connect Kibana to the `nyc_taxi_trips` index in Elasticsearch (autocreated in step 1)`
    * Click the **Management** tab >> **Index Patterns** tab >> **Create New**. Specify `nyc_taxi_trips` as the index pattern name and click **Create** to define the index pattern. (Leave the **Use event times to create index names** box unchecked and the Time Field as @timestamp)
    * If this is the only index pattern declared, you will also need to select the star in the top upper right to ensure a default is defined. 
* Load sample dashboard into Kibana`
    * Click the **Management** tab >> **Saved Objects** tab >> **Import**, and select `nyc_taxi_kibana.json`
    * On import you will be asked to overwrite existing objects - selecx-special/nautilus-clipboard
copy

t "Yes, overwrite all". Additionally, select the index pattern `nyc_visionzero` when asked to specify a index pattern for the dashboards.
* Open dashboard
    * Click on **Dashboard** tab and open `NYC Taxi Trips` dashboard
    
