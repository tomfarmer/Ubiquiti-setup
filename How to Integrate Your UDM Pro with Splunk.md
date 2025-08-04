# How to Integrate Your UDM Pro with Splunk (GUI Version)

This guide will walk you through setting up your Ubiquiti Dream Machine Pro (UDM Pro) to send logs to Splunk and categorize them using a custom Splunk app.

---

## Step 1: Install the Ubiquiti Add-on for Splunk

The add-on provides the necessary parsing rules and configurations. The easiest method is to install it directly from the GitHub repository.

1.  Navigate to the add-on's GitHub repository: [https://github.com/fwijnholds/TA-ubiquiti](https://github.com/fwijnholds/TA-ubiquiti)
2.  Click the green **`<> Code`** button and select **Download ZIP**.
3.  Unzip the downloaded file. You will have a folder named something like `TA-ubiquiti-main`.
4.  Inside that folder is the app directory, also named `TA-ubiquiti`.
5.  Compress the inner `TA-ubiquiti` directory into a `.tgz` or `.tar.gz` archive.
6.  In your Splunk UI, navigate to **Apps** > **Manage Apps**.
7.  Click the **Install app from file** button.
8.  Choose the `.tgz` file you just created and click **Upload**.

## Step 2: Configure Splunk for Data Ingestion

Before sending data, you need to tell Splunk where to put it.

### 2.1. Create Splunk Indexes

We need a primary "landing" index and several final destination indexes for sorted data.

1.  In Splunk, navigate to **Settings** > **Indexes**.
2.  Click **New Index** and create the following indexes one by one. You only need to provide the **Index Name** and can leave other settings at their defaults.
    * `ubnt` (This will be the initial landing index)
    * `ubnt_security`
    * `ubnt_network`
    * `ubnt_dhcp`
    * `ubnt_dns`

### 2.2. Create the Data Input

1.  In Splunk, navigate to **Settings** > **Data Inputs**.
2.  Select **UDP** and click **New Local UDP Port**.
3.  Fill out the form:
    * **Port:** Enter an unused port number. `8514` is a common choice for syslog.
    * **Source Name:** Click **Set Source Type** > **Manual** and type in `ubnt`.
4.  Click **Next**.
5.  On the next screen:
    * **Index:** Choose the **`ubnt`** index you created earlier.
    * **App Context:** Select `TA-ubiquiti`.
6.  Click **Review**, then **Submit**.

---

## Step 3: Configure Your UniFi Controller to Send Logs

Now, tell your UDM to send its logs to Splunk.

1.  Log in to your UniFi Network Application.
2.  Go to **Settings** > **CyberSecure** > **Traffic Logging**.
3.  Find the **Activity Logging (Syslog)** section.
4.  Enable **SEIM Server**.
    * **Server Address:** Enter the IP address of your Splunk server.
    * **Port:** Enter the same port you configured in Splunk (e.g., `8514`).
5.  Enable **Flow Logging** and select `All Traffic`.
6.  Under **Contents**, select all the log types you wish to forward (e.g., `Gateway`, `Security Detections`, `Firewall Default Policy`, etc.).
7.  Click **Apply Changes**.

## Step 4: Restart Splunk

Select **Settings** >  **Server Controls** > **Restart Splunk**

## Step 5: Check if its working

At this point, data should be flowing into your `ubnt` index in Splunk. Check by running the search below in your Splunk search bar. If you see data it worked!

```
index=ubnt
```

