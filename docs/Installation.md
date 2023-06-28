# Configuration

- [Configuration](#configuration)
  - [Configuration for Device Scanner Service](#configuration-for-device-scanner-service)
  - [Configure PLC Connection](#configure-plc-connection)
    - [Configure Databus](#configure-databus)
    - [Configure OPC UA Connector](#configure-opc-ua-connector)
  - [Configure Machine Insight](#configure-machine-insight)

To run the Machine Insight application, all the following applications must be deployed and configured in **the same IED**:

- Device Scanner Service (for scan functionality)
- Databus (for machine status feature)
- OPC UA Connector (for machine status feature)
- Machine Insight Configurator
- Machine Insight

## Configuration for Device Scanner Service

The Device Scanner Service is **optional** but required to be able to use the **scan functionality** in Machine Insight Configurator. It requires a specific network configuration called **Layer-2-Access** on the IED to work properly. Here you define an address pool used by Docker to configure a container IP address for the Device Scanner Service.

To configure the Layer-2-Access, open the UI of the IED and in the menu go to Settings > Connectivity > LAN Network. For the network interface, that is connected to the PLC, the Layer-2-Access must be configured. Click the corresponding edit icon for that interface and add the needed.

![Configure device LAN](/docs/graphics/Configure_Device_LAN.PNG)
![Confiture_Device_Layer_2_Access](/docs/graphics/Configure_Device_Layer_2_Access.PNG)

Make sure the Device Scanner Service is running on the IED.

## Configure PLC Connection

The IE Databus is **optional** but required to be able to use the **machine status feature** in Machine Insight Configurator. To read data from the PLC, we will use the OPC UA Connector to establish a connection via OPC UA and publish the PLC data on the Databus.

In order to build this infrastructure, these apps must be configured properly:

- Databus
- OPC UA Connector

Hint: Username and password should be the same for all system apps, e.g. "edge" / "edge".

### Configure Databus

In your IEM open the IE Databus and launch the configurator.

Add a user with this topic:
`"ie/#"`

![databus](/docs/graphics/Databus.PNG)

Deploy the configuration.

### Configure OPC UA Connector

In your IEM open the OPC UAgit Connector and launch the configurator.

Add a data source:

![OPC UA Connector Data Source](/docs/graphics/OPCUA_Connector_Data_Source.PNG)

Add the needed tag for the machine status:

![opcua_connector_config](/docs/graphics/OPCUA_Connector_Configuration.PNG)

Edit the settings:

![opcua_connector_settings](/docs/graphics/OPCUA_Connector_Settings.PNG)

Deploy and start the project.

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
