# Salesforce Examples

The following examples will use a Salesforce Connected App for communication between the Imp and Salesforce.  These examples require you to have an [Electric Imp developer account](https://impcentral.electricimp.com/login), the Electric Imp BlinkUp app, a [Salesforce developer account](https://developer.salesforce.com/signup?d=70130000000td6N), and the hardware listed below.

Please note these instructions are not actively updated. If any errors are found please submit pull requests to help us maintain these examples. 

## Hardware

The harware used in these examples is no longer available to purchase. If you already have an April breakout board with tails you can follow the instructions below, otherwise please get started with the Smart Refrigerator example found in the *SmartRefrigerator* folder. 

These examples will use an Imp 001, an April breakout board, and an Environmental Sensor tail to collect data. 

If you need guides to help you get started with Electric Imp visit the [Dev Center](https://developer.electricimp.com/gettingstarted).


## Getting Started

Create a product and developer device group in impCentral, and assign your device to the device group. Choose the example you wish to use, *Open a Case*, *Custom Object - Create a Record*, *Custom Object - Update a Record*.  Copy and paste the corresponding example code into the agent and device coding windows.  Select your device and make a note of your agent url, which can be found at the top of your model's agent code.  It will look something like this ```"https://agent.electricimp.com/szPc0sLfAqlu"```.  You will need the agent url when creating your connected app in Salesforce.


### Creating a Connected App in Salesforce

Step by step instructions to create a Connected App:

  1. Log into Salesforce and click on the **Setup** tab in the top right navigation menu
  2. In the sidebar under **Build** unfold the **Create** menu and select **Apps**
  3. At the bottom of the page under **Connected apps** click the **New** button
  4. In the **New Connected App** form fill in:
    - Basic Information
      - Connected App Name
      - API Name
      - Contact Email
    - API (Enable OAuth Settings)
      - Check the *Enable OAuth Settings* Box
      - Callback URL - this should be your agent url
      - Selected OAuth Scopes
        - Select *Access and manage your data (api)*
        - then click *Add*
    - When above info is filled out click **Save**
  5. You will be redirected to the *Connected App Name - your app name* page
    - Make a note of your Consumer Key (you will need to enter this into your agent code)
    - Click on Consumer Secret *Click to reveal*
    - Make note of your Consumer Secret (you will need to enter this into your agent code)

Open the Electric Imp IDE & select your device.  Find the *SALESFORCE CONSTANTS* section in the Agent code and enter your Consumer Key and Consumer Secret.

**Note:** These instructions were written in March 2016 and may not be up to date with the latest Salesforce web portal.

## Open a Case Example

In this example the Imp will open a case in Salesforce if the agent determines readings to be outside given ranges.  The device takes a temperature, humidity, and ambient light reading every 3 seconds then passes the readings to the agent.  The agent runs some logic to determine if the temperature has gone above 29°C or below 20°C, or if the ambient light has gone below 20 lux.  If any of these conditions have been met a case will be opened in Salesforce with the details of the event that triggered the case.  No further setup is requires for this exmple.  Please skip to [launch your app](#launching-app) section.


## Custom Object Examples

There are two examples using an Imp and a Salesforce custom object.  The **Create Custom Object** example creates a record everytime new data is available.  The **Update Custom Object** example a single record is created and then updated it everytime new data is available.   In both examples the device takes a temperature, humidity, air pressure, and ambient light reading every 5 minutes then passes those readings to the agent.  The agent then either *creates* or *updates* a record in Salesforce with the new readings.

### Creating a Custom Object in Salesforce

These examples take a bit more setup in Salesforce.  You will need to create a custom object with fields that correspond to each reading.  Step by step instructions for creating a Custom Object:

1. Log into Salesforce and click on the **Setup** tab in the top right navigation menu
2. In the sidebar under **Build** unfold the **Create** menu and select **Objects**
3. At the top of the page click the **New Custom Object** button
4. In the **New Custom Object** form fill in:
    - Custom Object Information
      - **Label** - for example *Env_Tail_Reading*
      - **Plural Label** - for example *Env_Tail_Readings*
      - **Object Name** - for example *Env_Tail_Reading*
    - Enter Record Name Label and Format
      - **Record Name** - for example *Reading Id*
      - **Data Type** select **Auto Number**
      - **Display Format** - for example *R-{0000}*
      - **Starting Number** - 0
    - When above info is filled out click **Save**
5. On the **Custom Objects Page** click on your object name
6. You will be redirected to the *Custom Object - your object name* page <br> You will repeat step 7 four times to add fields for each sensor reading collected <br> The **Field Name** must match the data table from the device. The **Field Names** in the exmple code are: **temperature**, **humidity**, **pressure**, and **amb_lx**
7. At the bottom of the page under **Custom Fields & Relationships** click the **New** button
    - Step 1 *Data Type*
      - Select **Number**
      - then click **Next** button
    - Step 2 of 4
      - Enter **Field Label** - for example *temperature*
      - Enter **Length** - for example *4*
      - Enter **Decimal Places** - for example *2*
      - Enter **Field Name** - for example *tempertature*
      - Enter **Description** - for example *Temperature reading in °C*
      - then click **Next** button
    - Step 3 of 4
      - click **Next** button
    - Step 4 of 4
      - click **Save & New** <br>
      **Repeat** Steps 1-4 for humidity, pressure and amb_lx
8. We need to create one more Field the *Device Id field* <br>**Note**: Step 2 is slightly different for **Update Custom Object** and **Create Custom Object** examples.
    - Step 1 *Data Type*
      - Select **text**
      - then click **Next** button
    - Step 2 of 4
      - Enter **Field Label** enter **deviceId**
      - Enter **Length** - for example *32*
      - Enter **Field Name** enter **deviceId**
    - check **Required** <br>For ***Updating a Custom Object*** example **ONLY** do these 3 additional steps.
        - check **Unique**
        - check **Test "ABC" and "abc" as different values (case sensitive)**
        - check **External ID**
      - then click **Next** button
    - Step 3 of 4
      - click **Next** button
    - Step 4 of 4
      - click **Save**
9. You will be redirected to the *Custom Object - your object name* page
    - Make a note of your **API Name** (you will need to enter this into your agent code)

Open the Electric Imp IDE & select your device.  Under the *APPLICATION CODE* find the *RUNTIME VARIABLES* section in the Agent code (this will be towards the bottom) and set the **obj_api_name** variable to your **API Name**.  If you named your Custom Object *Env_Tail_Reading* you will be able to skip this step. You are now ready to [launch your app](#launching-app).

**Note:** These instructions were written in March 2016 and may not be up to date with the latest Salesforce web portal.


## Launching App

These examples use OAuth 2.0 for authentication, so the agent has been set up as a web server to handle the log in.
Go to the Electric Imp IDE and select your device from the sidebar for the final setup steps.

- Hit **Build and Force Restart** to save and launch the code
- Click on the agent url (at the top of the agent coding window) to launch the log in page
- Log into salesforce

Your App should now be up and running.  You can monitor the device logs in impCentral, or log into Salesforce web prortal to see updates there.
