# Configurations

- [Configurations](#configurations)
  - [Configure Databus](#configure-databus)
  - [Configure SIMATIC S7+ Connector](#configure-simatic-s7-connector)
  - [Configure IIH Essentials](#configure-iih-essentials)
  - [Configure Machine Insight](#configure-machine-insight)
    - [Migration](#migration)
    - [Configuration](#configuration)

To run the Machine Insight application, the following applications must be deployed on **the same IED**:

- Databus (MQTT broker)
- SIMATIC S7+ Connector (provide machine status parameter, retrieve alarms)
- Common Configurator (configure SIMATIC S7+ connector)
- Registry Service (register SIMATIC S7+ connector)
- Common Import Converter (import S7+ tags)
- IIH Essentials (define asset structure, create alarm channels)
- Machine Insight (dashboards with machine data)

> HINT: If you upgraded from an older version of Machine Insight to V2.0, it is necessary to manually uninstall the app Machine Insight Configurator after migration. This app is not needed any more.

## Configure Databus

The **Databus** acts as MQTT broker and is essential to exchange data between a PLC and the IED via the SIMATIC S7+ Connector. You need to create an user and one or more topics in the Databus configuration, which cover the process data.

Therefore follow these steps:

- open the Industrial Edge Management (IEM)
- go to 'Data Connections' > Databus
- select the corresponding IED and launch
- create a new user (`edge`/`edge`) with the dedicated topic `ie/#` and set the permissions to 'Publish and Subscribe'
- deploy the configuration and wait for the job to be finished successfully

![databus](/docs/graphics/Databus.PNG)

## Configure SIMATIC S7+ Connector

To read data from the PLC, the **SIMATIC S7+ Connector** must be used. The connector configuration is done via the **Common Configurator**. Furthermore the apps **Registry Service** and **Common Import Converter** need to be launched on the IED.

The Common Configurator publishes the connector data on the Databus. Therefore, you must enter the Databus credentials ('edge'/'edge') within the Common Configurator:

- open the IED web interface
- open the app Common Configurator
- go to the tab 'Settings' and select the menu 'Databus credentials'
- enter the databus service name: `ie-databus:1883`
- in tab 'Data Publisher settings' enter the databus user name and password (`edge`/`edge`)
- in tab 'Data Subscriber settings' enter the databus user name and password (`edge`/`edge`)
- save the settings

![IIHDatabusSettings](/docs/graphics/IIHDatabusSettings.png)

As soon as the SIMATIC S7+ Connector is installed and started on the same IED as the Common Configurator, the connector is visible within the configurator. In this example we want to configure a S7+ connection to a CPU 1515F-2. It is required to have an SIMATIC SCADA Export of the dedicated TIA project available (Export.zip).

You can find detailled information how to use the SIMATIC SCADA Export for TIA Portal [here](https://support.industry.siemens.com/cs/document/109748955/simatic-scada-export-for-tia-portal?dti=0&lc=en-WW).

- go to the tab 'Get data'
- select the SIMATIC S7+ Connector
- switch to tab 'Tags'
- add a new data source
- choose 'add from file'
- select the Export.zip from the SIMATIC SCADA Export of the TIA project
- add a name and the IP address of the PLC
- select the option 'Provide PLC alarms'
- click 'Continue to Select tags'

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

> IMPORTANT: The alarms can only be activated when creating the data source. It is not possible to add them afterwards!

## Configure IIH Essentials

Within **IIH Essentials** you can define the asset structure for your plant: 

- open the app IIH Essentials
- create the asset hirarchy accordingly

The app gathers all the necessary process data and saves it for a configured period of time. In this case we need to get data out of the SIMATIC S7+ Connector, therefore this must be activated:

- go to the tab 'Settings' and select 'Databus settings'
- enter the databus service name: `ie-databus:1883`
- enter the user name and password (`edge`/`edge`) and save
- go to the tab 'Connectors'
- select the 'SIMATIC S7 Plus Connector'
- activate and save

![IIHEssentialsAdaper](/docs/graphics/IIHEssentialsAdapter.png)

To add the **PLC parameter** for the machine state, proceed as following:

- go to tab 'Assets & Connectivity'
- select the dedicated asset
- clickt 'Create first variable'
- select the 'SIMATIC S7 Plus Connector'
- select the machine state parameter
- add variable

![IIHEssentialsParameter](/docs/graphics/IIHEssentialsParameter.png)

For this asset we also need to create an **alarm channel** for getting the alarm data out of the PLC:

- within the asset, select the tab 'Alarms'
- select 'Create first alarm channel'
- enter a name for the alarm channel
- select the 'SIMATIC S7 Plus Connector'
- select only one source from your configured S7+ Connector PLCs
- select a proper alarm type

![IIHEssentialsAlarmChannel](/docs/graphics/IIHEssentialsAlarmChannel.png)

> IMPORTANT: It is essential to have **only one source selected** on the alarm channel. Otherwise no device status will be displayed within Machine Insight!

## Configure Machine Insight

### Migration

Machine Insight V2.0 shows an entry page if you open the UI. In case you updated an existing app version to V2.0 you are able to follow a migration wizzard.

![MachineInsightEntryPage](/docs/graphics/MachineInsightEntryPage.png)

In case of a new Machine Inight installation, no migrtion is necessary and only the 'Configuration' option will appear in the entry page.

**Step Get Data**

Click on the ‘Get Data’ button to open the Common Configurator for configuring the SIMATIC S7+ Connector. See chapter [Configure SIMATIC S7+ Connector](#configure-simatic-s7-connector).

![MachineInsightMigration1](/docs/graphics/MachineInsightMigration1.png)

**Step Alarm Channel**

Click on the ‘Alarm Channel’ button to open IIH Essentials for defining the asset hierarchy and the alarm channels. See chapter [Configure IIH Essentials](#configure-iih-essentials).

![MachineInsightMigration2](/docs/graphics/MachineInsightMigration2.png)

**Step Data Migration**

Here you are able to transfer previously configured PLCs to the new version:

- select all the PLCs that need to be transfered
- select a dedicated asset for each PLC for mapping
- click 'Validate' (the selected assets should have a source and alarm channel configured)
- after successful validation click 'Start Migration'
- data of selected PLCs is transferred to mapped asset (running in background)

![MachineInsightMigration3](/docs/graphics/MachineInsightMigration3.png)

> IMPORTANT: This step is optional and can be performed only once! Therefore ensure that all needed PLCs are selected!

**Step Configuration**

Click on the 'App Configuration' button to switch to the configuration tab of Machine Insight. See chapter [Configuration](#configuration).

![MachineInsightMigration4](/docs/graphics/MachineInsightMigration4.png)

### Configuration

Within Machine Insight you are able to configure a **status mapping** for the machine state, which is later necessary to visualize the status in a Gantt chart:

- switch to tab 'Configuration' within the left-side menu
- click 'Go to Status mappings' and create a new mapping
- enter a name and a description (optional)
- setup the mapping by assigning each value of the machine status to a dedicated text label with unique color
- save the mapping

![MachineInsightStatusMapping](/docs/graphics/MachineInsightStatusMapping.png)

Machine Insight is based on the asset structure that was defined within IIH Essentials. Finally, you need to configure the assets for which a machine data dashboard should be shown within Machine Insight.

- switch to tab 'Configuration' within the left-side menu
- click 'Go to Asset Configuration'
- navigate to the asset for which you want to create a dashboard
- click 'Find parameter' to assign the belonging machine status variable
- select the dedicated status mapping via the drop down box
- save the settings

![MachineInsightAssetConfig](/docs/graphics/MachineInsightAssetConfig.png)

Now Machine Insight will automatically generate one dashboard per configured assset. Please find more information in the [Usage](/README.md#usage) chapter.
