# Google SecOps Native Dashboards for Corelight

## Overview

This guide provides step-by-step instructions for setting up and utilizing Google SecOps Native Dashboards to monitor and analyze network traffic data. By leveraging the capabilities of Chronicle Backstory, these dashboards deliver scalable, real-time insights across your organization's infrastructure, enhancing your security operations and visibility.

## Pre-requisites

Before you begin, ensure the following prerequisites are met:

- **Google SecOps Platform Access:** You must have an active account with access to the Google SecOps platform.
- **Google SecOps Native Dashboards Access:** Ensure you have the necessary permissions to access and create custom dashboards in the native dashboards section.
- **GitHub Repository Access:** Confirm that you can access the [CorelightForSecOps] GitHub repository, which contains the required dashboard configuration files.

## Installation & Configuration

### Create a reference list (For identifying Internal/External IPs)

1. Head over to the Google SecOps Platform
2. From the left side menu got to Detections -> Lists Section
3. Now a List manager popup will appear.
4. For creating a new list click on Create button.
5. Select the syntax type of the list as CIDR and name the title of the list as internal_cidr_list.
6. Also, add the IP range (as per your requirement) in the last section.
7. Finally, Click on Save and your list will be created.


### Deploy Dashboards from GitHub Repository

To set up the SecOps Native Dashboards, you'll need to deploy them from the [CorelightForSecOps] GitHub repository. Follow these steps to do so:

### Step 1: Download Dashboard Configuration Files from GitHub

- Navigate to the [CorelightForSecOps] GitHub repository.
- Download the JSON configuration files for the dashboards to your local machine.

### Step 2: Open Google SecOps and Navigate to Native Dashboards

- Launch the Google SecOps platform in your preferred browser.
- Navigate to the Native Dashboards section in the interface.

### Step 3: Import the downloaded dashboards

- In the top-right corner, click the downward arrow next to the New Dashboard button.
- Select Import from JSON from the dropdown menu.

### Step 4: Upload the Desired Dashboard JSON File

- A dialog will prompt you to upload a file.
- Click on Upload dashboard files.
- Browse your local system and select the desired JSON file from the downloaded configurations.
- Click Import to complete the upload.

### Step 5: Rename the Imported Dashboard (Optional)

- Open the imported dashboard.
- Click the Edit button.
- Then click Edit Details.
- In the popup dialog, update the dashboard name as desired.
- Click Save to apply the new name.

### Step 6: Access and View Your Imported Dashboard

- Use the search bar to locate the newly imported dashboard by name.
- Click on the dashboard to view its contents, including charts, graphs, and real-time data visualizations.


