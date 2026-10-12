# Google SecOps Dashboards for Corelight

## Overview

This guide provides step-by-step instructions for setting up and utilizing **Google SecOps Dashboards** to monitor and analyze network traffic data. By leveraging the capabilities of Chronicle Backstory, these dashboards deliver scalable, real-time insights across your organization's infrastructure, enhancing your security operations and visibility.

## Pre-requisites

Before you begin, ensure the following prerequisites are met:

- **Google SecOps Platform Access:** You must have an active account with access to the Google SecOps platform.
- **Google SecOps Dashboards Access:** Ensure you have the necessary permissions to access and create custom dashboards in the dashboards section.
- **GitHub Repository Access:** Ensure you have access to the [CorelightForSecOps](https://github.com/corelight/CorelightForSecOps/tree/main) GitHub repository, which includes the necessary dashboard configuration files. Also, verify that the required parsers are enabled and logs are sent to Google Security Operations using Corelight Sensor which is stated [here](https://github.com/corelight/CorelightForSecOps/blob/main/README.md).

## Installation & Configuration

### Reference lists and data tables required by dashboards

Some dashboards look up values in Google SecOps reference lists or data tables. Create these before you import the dashboards that use them. If one is missing, the charts that use it return no data.

The names are case-sensitive and must match the table below exactly, because the dashboard queries refer to them by name (for example, `%corelight_use_cases.use_case`).

| Name | Type | Used by dashboard(s) | Contents |
|------|------|----------------------|----------|
| `internal_cidr_list` | Reference list (CIDR) | Security Posture, Secure Channel Insights, Name Resolution Insights, Files, SSL | Your internal IP CIDR ranges (see the section above). |
| `corelight_ai_apps` | Reference list (String) | AI Detection & Response | Identifiers of the GenAI applications to track. |
| `corelight_geo_attr_countries` | Data table | AI Detection & Response | Maps ISO country codes to country names. Columns: `iso2`, `country`. |
| `corelight_use_cases` | Data table | Anomaly Detection, Anomaly Detection Details | Describes each Corelight anomaly detection use case. Columns: `use_case`, `description`, `significance`, `baseline_behavior`, `anomalous_behavior`. |

#### Create a reference list (For identifying Internal/External IPs)

1. Head over to the **Google SecOps** Platform

![image](https://github.com/user-attachments/assets/874b4098-aba6-4725-8b90-710cdf26c7d7)

2. From the left side menu go to **Detections -> Lists** Section

![image](https://github.com/user-attachments/assets/c52a8abe-918e-4248-9e04-c6673d9a0572)

3. Now a List manager popup will appear. Click on **Create** button for creating a new List.

![image](https://github.com/user-attachments/assets/184a9b83-357f-48f2-b643-0ed9889ed6ee)

4. In the popup, select the **Syntax type** of the list as **CIDR** and name the title of the list as **internal_cidr_list**. Also, add the **IP CIDR range** (as per your requirement) in the last section.

![image](https://github.com/user-attachments/assets/461f3d11-c773-4560-b54f-6087e3994024)

5. Finally, Click on **Save Edits** from the bottom right corner and your CIDR list will be created.

![image](https://github.com/user-attachments/assets/4af98b59-a14b-4604-be6f-87571c066235)

#### Create the `corelight_ai_apps` reference list

The **AI Detection & Response** dashboard only shows Corelight `conn` events whose application identifier is in this list.

1. In Google SecOps, go to **Detections -> Lists** and click **Create**.
2. Set **Syntax type** to **String** and set the title to **corelight_ai_apps**.
3. Add one GenAI application identifier per line and click **Save Edits**.

The dashboard's Risk Tier and Corporate Origin charts classify the identifiers below. Start from this list and add or remove entries to suit your environment:

<details>
<summary>Suggested <code>corelight_ai_apps</code> entries</summary>

```
01ai-yi
adept-ai
adobe-firefly
ai21
alibaba-qwen
amazon-q
anthropic
arthur-ai
baichuan-ai
baidu-ernie
baseten
braintrust
bytedance-doubao
character-ai
chatgpt
chroma
claude
codeium
cohere
crewai
datarobot
deepl
deepseek
elevenlabs
fathom
fireflies-ai
fireworks-ai
forefront-ai
github-copilot
google-gemini
gpt4all
groq
helicone
iflytek-spark
inflection-pi
kagi
krisp
lakera
leonardo-ai
lepton-ai
litellm
mem-ai
meta-ai
microsoft-copilot
midjourney
minimax
mistral
modal
moonshot
notion-ai
nvidia-ngc
octoai
openai
openrouter
otter-ai
phind
pika
pinecone
poe
portkey
read-ai
replicate
runwayml
scale-ai
sensetime
sourcegraph-cody
stability-ai
stepfun
suno
synthesia
tabnine
tldv
together-ai
udio
wandb
weaviate
you-ai
zapier-ai
zhipu-ai
```

</details>

#### Create the data tables

Data tables are imported from a file. Supported formats are **CSV** (comma-separated values, `.csv`) and **TSV** (tab-separated values, `.tsv`). The first row of the file must be a header row whose column names match the columns listed below.

1. In Google SecOps, go to **Investigation -> Data Tables**.
2. Create a new data table and set its name to the exact name shown below.
3. You can either type data manually or import your `.csv` or `.tsv` file and save the data table.
4. Repeat for each data table.

**`corelight_geo_attr_countries`**

| Column | Description |
|--------|-------------|
| `iso2` | Two-letter ISO 3166-1 country code. It is matched against `target.ip_geo_artifact.location.country_or_region`. |
| `country` | Country name that the dashboard displays. |

Example (`corelight_geo_attr_countries.csv`):

```csv
iso2,country
US,United States
GB,United Kingdom
IN,India
```

> **Note:** The chart only shows connections whose destination country code is in this table, so include every country you want to see.

**`corelight_use_cases`**

| Column | Description |
|--------|-------------|
| `use_case` | Corelight anomaly use case name. It is matched against the `use_case` field of the `anomaly` log (`security_result.rule_name` in UDM). |
| `description` | What the use case detects. |
| `significance` | Why an anomaly from this use case matters. |
| `baseline_behavior` | What the use case learns during its baselining period. |
| `anomalous_behavior` | What behavior raises the anomaly. |

Example (`corelight_use_cases.csv`):

```csv
use_case,description,significance,baseline_behavior,anomalous_behavior
<use_case>,<description>,<significance>,<baseline_behavior>,<anomalous_behavior>
```

> **Note:** Several charts in **Anomaly Detection** (Anomalies Observed, Anomaly Summary, Detections per Anomaly Type over Time, Detection Breakdown by Anomaly Type and Source IP) and most panels in **Anomaly Detection Details** only show anomalies whose `use_case` has a row in this table. Add a row for every use case that is enabled on your sensors.


### Deploy Dashboards from GitHub Repository

To set up the SecOps Dashboards, you'll need to deploy them from the [CorelightForSecOps](https://github.com/corelight/CorelightForSecOps/tree/main) GitHub repository. Follow these steps to do so:

### Step 1: Download Dashboard Configuration Files from GitHub

- Navigate to the [CorelightForSecOps](https://github.com/corelight/CorelightForSecOps/tree/main) GitHub repository and head over to the [dashboards](https://github.com/corelight/CorelightForSecOps/tree/develop/dashboards) folder.
- Download the JSON configuration files for the dashboards to your local machine.

![image](https://github.com/user-attachments/assets/f0af3868-c901-4e82-80dc-69983e1139f3)

### Step 2: Open Google SecOps and Navigate to Dashboards

- Launch the Google SecOps platform in your preferred browser.
- Navigate to the Dashboards section in the interface.
- In the Dashboards section, you will see a list of custom dashboards.


![image](../images/dashboard1.png)
![image](../images/dashboard2.png)

### Step 3: Import the downloaded dashboards

- In the top-right corner, click the downward arrow next to the New Dashboard button.
- Select Import from JSON from the dropdown menu.

![image](https://github.com/user-attachments/assets/0e7f73ab-cdd3-4ba3-9770-f5b24f2895d8)

### Step 4: Upload the Desired Dashboard JSON File

- A dialog will prompt you to upload a file. Click on **Upload dashboard files**.
- Browse your local system and select the desired JSON file from the downloaded configurations.
- Click **Import** to complete the upload.

![image](https://github.com/user-attachments/assets/432124a1-e712-4467-adf2-821393c003be)

### Step 5: Rename the Imported Dashboard (Optional)

- When importing a dashboard from your local machine, after selecting the file in the pop-up window, click the **Edit** button next to the selected dashboard file and rename it according to your preference and click on **Save** button.
- Also, you can change the access of your dashboard to public or private based on your preferences.
- Lastly, click on **Import** after editing the name.

![image](../images/dashboard3.png)

![image](https://github.com/user-attachments/assets/e1b33f21-461c-4a8c-8eb8-ae37e1683b44)

### Step 6: Access and View Your Imported Dashboard

- Use the search bar to locate the newly imported dashboard by name.
- Click on the dashboard to view its contents, including charts, graphs, and real-time data visualizations.

![image](../images/dashboard4.png)

After clicking on the dashboard you imported, you will be able to view your dashboard based on your instance data.
