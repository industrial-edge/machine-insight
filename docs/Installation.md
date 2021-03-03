# Configuration

- [Configuration](#configuration)
  - [Configure Device Layer 2 access](#configure-device-layer_2_access)
  - [Configure PLC Connection](#configure-plc-connection)
    - [Configure Databus](#configure-databus)
    - [Configure S7 Connector](#configure-s7-connector)
  - [Configure Machine Insight](#configure-machine-insight)
    - [Configure Machine Insight Configurator](#configure-machine-insight-configurator)
    - [Configure Machine Insight Overview](#configure-maschine-insight-overview)

## Configure Device Layer 2 access

The device scanner requires a layer 2 access to enable the scanner of the devices in the Machine Insight.

Hint: Layer 2 access can only be configured for a new device, not later.

Open the management system and select "My Edge Devices" on the left side in the bar.

Click on "+ New Edge Device" on the upper right side.

Configure your Edge Device and click on "Next".

Click on the "+" button at the top right to configure the network interface.

![Configure_Device_New](graphics/Configure_Device_New.PNG)

Configure the network interface and the layer 2 access and click on "add".

![Confiture_Device_Layer_2_Access](graphics/Configure_Device_Layer_2_Access.PNG)

Confirm the device configuration with "Next" and with "Create".

## Configure PLC Connection

To read data from the PLC and provide the data, we will use S7 Connector to establish connection with the PLC via OPC UA.

The S7 Connector sends the data to the Databus, where the Data Service app can collect what is needed.

In order to build this infrastructure, these apps must be configured properly:

- Databus
- S7 Connector

### Configure Databus

In your IEM open the Databus and launch the configurator.

Add a user with this topic:
`"ie/#"`

![ie_databus_user](graphics/IE_Databus_User.PNG)

![ie_databus](graphics/IE_Databus.PNG)

Deploy the configuration.

### Configure S7 Connector

In your IEM open the S7 Connector and launch the configurator.

Add a data source:

![S7 Connector Data Source](graphics/S7_Connector_Data_Source.PNG)

Add needed tags:

![s7_connector_config](graphics/S7_Connector_Configuration.PNG)

Edit the settings:

![s7_connector_settings](graphics/S7_Connector_Settings.PNG)

Hint: Username and password should be the same for all system apps, e.g. "edge" / "edge".

Deploy and start the project.

## Configure Machine Insight

In your IED Web UI open the app Machine Insight Configurator.

Hint: Before the Machine Insight can be configured, the Device Scanner must be installed on the IED.

### Configure Machine Insight Configurator

Click "Add New Device" at the top of the left side.

![Machine_Insight_Configurator](graphics/Machine_Insight_Configurator.PNG)

Click on "Scan and Add" to automatically scan the network and see all devices on the network.

If you are looking for a specific device with name and IP, click on "Manually Add".

![Machine_Insight_Configurator_Add](graphics/Machine_Insight_Configurator_Add.PNG)

Enter the IP range in which the devices are to be searched.

Click on "Start Scan" and select the device on the left side by clicking on it.

To save, click on the "Save & Close" button at the bottom left.

![Machine_Insight_Configurator_Scan_Configuration](graphics/Machine_Insight_Configurator_Scan_Configuration.PNG)

Select a device and click on "Settings" at the top right.

![Machine_Insight_Configurator_Settings](graphics/Machine_Insight_Configurator_Settings.PNG)

To apply all settings, click on "Deploy" in the top right-hand corner.

To get to the Machine Insight view, click on "Go to App".

![Machine_Insight_Configurator_Settings_Deploy](graphics/Machine_Insight_Configurator_Settings_Deploy.PNG)

### Configure Machine Insight Overview

Select your device in the top left-hand corner to access the overview.

Here you can see the device status, notification icon and mapping status.

![Machine_Insight_Overview](graphics/Machine_Insight_Overview.PNG)
