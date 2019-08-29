# BWCE_Demo_Docker - STILL UNDER CONSTRUCTION!!
## Getting Started
To run this demo you will need the following:
- Base TIBCO BWCE Docker Image (currently 2.4.5)
  - Follow these instructions to build this image: https://docs.tibco.com/pub/bwce/2.4.5/doc/html/GUID-91EA80AA-08EF-4CB3-A6A7-E8551A441AC1.html
  - Tag the image as <i>tibco/bwce:2.4.5</i>
- Extended BWCE Image with MySQL Driver
  - Follow these instructions to build this image: https://docs.tibco.com/pub/bwce/2.4.5/doc/html/GUID-E1609C4C-BCA4-4D04-8E5B-503FE3166B89.html
  - Tag the image as <i>tibco/bwce-mysql:2.4.5</i>
- Running Instance of Consul
  - A simple Consul Server can be started using Docker
  - <i>docker pull progrium/consul</i>
  - <i>docker run -d -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h node1 progrium/consul -server -bootstrap -ui-dir /ui</i>
  - Once running navigate to http://localhost:8500/ui and confirm the server has started
- Maven Plugin (currently 2.3.0)
  - Be sure this is installed in the current TIBCO_HOME
  - Follow these instructions to install the plugin: https://github.com/TIBCOSoftware/bw6-plugin-maven/blob/master/README.md
- Instance of MySQL with username and password available and a schema named <i>ms_demo</i>

## Setting up Demo for Local Environment
### BW Project Setup
- Download or clone this Repository
- Import projects into BusinessWorks Studio
- Using the Search Menu, select the File... option
  - Containing Text: <i>192.168.1.94</i> 
  - File name patterns: <i>\*.\*</i>
  - Click <i>Replace...</i>
  - There should be 18 matches (as of Sept 2019)
  - Enter your IP address in the <i>With:</i> box and click <i>OK</i>
  - \*\* All of this assumes you are running the BWCE containers locally and that Consul is running on the same IP Address that the BWCE Containers will be running. If you are running it on a different machine or have some other unique set up, go to each Application Project and update the <i>docker-env.properties</i> files accordingly
- If you have NOT tagged the Base Image and the MySql Extension Image as indicated above, you need to update the <i>docker-dev.properties</i> file in each Application Project
  - Update the <i>bwdocker.from</i> property to match your MySQL Extension Image name
    - Note that the Quote Application does not need the MySQL image and can use the Base BWCE Image.
### MySQL Setup
- The Application BWCE_DemoSetup will take care of creating the tables and data for the Demo
- You need to capture the JDBC Connection string and the username/password
- You will use it in the next step
### Consul Setup
- Consul is used for 2 purposes in this Demo: Service Registration/Discovery and Configuration Management
- The Service Registration and Discovery use is not optional. The Services are configured this way
- The Configuration Management use however is optional. Configuring the Key/Value pairs in Consul is a bit tedious so these properties are also exposed in the <i>docker-env.properties</i> file
  - If you would like to use Consul for the property values, go to the Key/Value section in Consul and set up the Key/Value pairs     
    - You need to create a folder for each Application in the form of \<ApplicationName\>/Docker
      - BWCE_PriceAPI/Docker
      - BWCE_ProductAPI/Docker
      - BWCE_InventoryAPI/Docker
      - BWCE_DiscountAPI/Docker
      - BWCE_Quote/Docker
      - BWCE_DemoSetup/Docker
    - Create the following Key/Value pairs for each Application
      - BWCE_Demo.credentials.uri <-- Set this to the MySQL JDBC URL (e.g. jdbc:mysql://mysql-server-ip/ms_demo)
      - BWCE_Demo.credentials.username <-- Set this to the MySQL username
      - BWCE_Demo.credentials.password <-- Set this to the MySQL password (This is not secure and used for demo purposes only)
      - SERVICE_NAME <-- Set this to <i>BWCE_Demo</i>
  - If you would like to skip the Consul Key/Value pair setup, simply update all <i>docker-env.properties</i> files and set the appropriate values for the four variables above (SERVICE_NAME is already set in the property files)
    - You will see an exception in the Container Log file saying it can not read properties but the values will be picked up from the properties file and the container will function as normal
### Running the Demo
- Within the Studio Environment, Right click on each Application Parent Folder (e.g. BWCE_DemoSetup.parent)
