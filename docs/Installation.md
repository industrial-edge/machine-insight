# Configuration

- [Configuration](#configuration)
  - [Configure Databus](#configure-databus)
  - [Configure SIMATIC S7+ Connector via Common Configurator](#configure-simatic-s7-connector-via-common-configurator)
  - [Configure IIH Essentials](#configure-iih-essentials)
  - [Configure Machine Insight](#configure-machine-insight)

To run the Machine Insight application, the following applications must be deployed and configured on **the same IED**:

- Databus (MQTT broker)
- SIMATIC S7+ Connector (provide machine status parameter, retrieve alarms)
- Common Configurator (configure SIMATIC 7+ connector)
- Registry Service (register SIMATIC S7+ connector) > no config necessary
- Common Import Converter (import S7+ tags) > no config necessary
- IIH Essentials (define asset structure, create alarm channels)
- Machine Insight

## Configure Databus

The **Databus** acts as MQTT broker and is essential to exchange data between a PLC and the IED via a connector. You need to create an user and one or more topics in the Databus configuration, which cover the process data.

Therefore follow these steps:

- open the Industrial Edge Management (IEM)
- go to 'Data Connections' > Databus
- select the corresponding IED and launch
- create a new user ('edge'/'edge') with the dedicated topic `ie/#` and set the permissions to 'Publish and Subscribe'
- deploy the configuration and wait for the job to be finished successfully

![databus](/docs/graphics/Databus.PNG)

## Configure SIMATIC S7+ Connector via Common Configurator

To read data from the PLC, the **SIMATIC S7+ Connector** must be used. The connector configuration is done via the **Common Configurator**. Furthermore the apps **Registry Service** and **Common Import Converter** need to be launched on the IED.

The Common Configurator publishes the connector data on the Databus. Therefore, you must enter the Databus credentials within the Common Configurator:

- open the IED web interface
- open the app Common Configurator
- go to the tab 'Settings' and select the menu 'Databus credentials'
- enter the databus service name: 'ie-databus:1883'
- in tab 'Data Publisher settings' enter the databus user name and password ('edge'/'edge')
- in tab 'Data Subscriber settings' enter the databus user name and password ('edge'/'edge')
- save the settings

![IIHDatabusSettings](/docs/graphics/IIHDatabusSettings.png)

As soon as the SIMATIC S7+ Connector is installed and started on the same IED as the Common Configurator, the connector is visible within the configurator. In this example we want to configure a S7+ connection to a CPU 1515F-2. It is required to have an SIMATIC SCADA export of the dedicated TIA project available (Export.zip).

- go to the tab 'Get data'
- select the SIMATIC S7+ Connector
- switch to tab 'Tags'
- add a new data source
- choose 'add from file'
- select the Export.zip from the SIMATIC SCADA Export of the TIA project
- add a name and the IP address of the PLC
- select the option 'Provide PLC alarms'
- click 'Continue to "Select tags"'

![DataSource](/docs/graphics/IIHDataSource.png)

- select an acquisition cycle and a access mode in the drop down fields
- select the parameter that represents the machine state
- click 'Import'

![DataSource2](/docs/graphics/IIHDataSource2.png)

For writing the tag values onto the MQTT databus you need to activate and confirm the 'Publish on the databus' option for each tag:

- select the parameter
- click on the edit icon of the parameter
- activate the option 'publish on the databus'
- select an acquisition cycle 
- accept the settings

![DataSource3](/docs/graphics/IIHDataSource3.png)

Select the newly created PLC including all the tags and click 'Deploy' to save the configuration and start the project.

> The alarms can only be activated when creating the data source. It is not possible to add them afterwards!

## Configure IIH Essentials

Within **IIH Essentials** you can define the asset structure for your plant: 

- open the app IIH Essentials
- create the asset hirarchy accordingly

The app gathers all the necessary process data and saves it for a configured period of time. In this case we want to get data out of the S7+ Connector, therefore this must be activated:

- go to the tab 'Settings' and select 'Databus settings'
- enter the databus service name: `ie-databus:1883`
- enter the user name and password ('edge'/'edge') and save
- go to the tab 'Connectors'
- select the 'SIMATIC S7 Plus Connector'
- activate and save

![IIHEssentialsAdaper](/docs/graphics/IIHEssentialsAdapter.png)

To add the PLC parameter for the machine state, proceed as following:

- go to tab 'Assets & Connectivity'
- select the dedicated asset
- clickt 'Create first variable'
- select the SIMATIC S7 Plus Connector
- select the machine state parameter
- add variable

![IIHEssentialsParameter](/docs/graphics/IIHEssentialsParameter.png)

For this asset we also need to create an alarm channel for getting the alarm data out of the PLC:

- within the asset, select the tab 'Alarms'
- select 'Create first alarm channel'
- configure it as following and save

![IIHEssentialsAlarmChannel](/docs/graphics/IIHEssentialsAlarmChannel.png)

> It is essential to have **only one source selected** on the alarm channel. Otherwise no device status will be displayed within Machine Insight!

## Configure Machine Insight

The Machine Insight Configurator provides the user interface to configure the Machine Insight application for data extraction from field devices and to collect machine status from a different application and store it in database.

In your IED open the Machine Insight Configurator.

Click "Add New Device" and choose "Scan and Add" to automatically scan the network for all available devices in the network. If you are looking for a specific device with name and IP, choose "Manually Add".

![Machine_Insight_Configurator](/docs/graphics/Machine_Insight_Configurator.PNG)

In the scan configuration window enter the following information:

- Service Name: industrial-device-scanner (should be prefilled)
- Port: 50020 (should be prefilled)
- IP range From and To: enter IP adresses in which to be scanned for devices

Click on "Start Scan" and select a device on which to connect to.

![Machine_Insight_Scan](/docs/graphics/Machine_Insight_Scan.png)

To be able to subscribe to the dedicated machine status data from the Databus, expand the 'Configure Global Device Settings' and under section 'Databus Configuration' enter your databus username and password.

![Machine_Insight_Global_Config](/docs/graphics/Machine_Insight_Global_Config.png)

For the added device, go to column 'Status Mapping' and click 'Create new' to create a new mapping for the machine status. Assign a name to the status mapping and define proper values and labels for this status mapping.

![Machine_Insight_Status_Mapping](/docs/graphics/Machine_Insight_StatusMapping.png)

Back in the global settings window, select the just created status mapping for the device. To assign a tag for the machine state, click the folder icon under 'Actions'.

If you choose the meta data topic for the S7 Connector and the according connection, all available tags are listed. Select the proper tag for the machine status.

![Machine_Insight_Tags](/docs/graphics/Machine_Insight_Tags.png)

Finally select the device and continue to 'Settings' at the top right.

![Machine_Insight_Overall_Config](/docs/graphics/Machine_Insight_Overall_Config.png)

Under tab 'General' you can change general settings like language or the time zone. Under tab 'Diagnostic Data' you can define the number of diagnostic events that are retained. Furthermore you can select the diagnostic events data type for which the data is extracted (Diagnostic Buffer OR Alarms).

To apply all settings, click on "Update" in the top right corner.

To open the Machine Insight view, you can click on "Go to App" or open the UI of the application via the IED.
