**🚀 AI-Powered Lead Recommendation Engine**

**Intelligent Pure Storage Product Recommendations & Automated Sales Enablement**

---

**📋 Overview**

This automated solution transforms the lead engagement process by intelligently matching Pure Storage product recommendations to client inquiries and generating personalized outreach emails.  
 Built on **n8n’s enterprise-grade automation platform**, it seamlessly integrates **Salesforce CRM**, **Google Sheets**, and **AI language models** to deliver timely, relevant recommendations — accelerating sales cycles and improving conversion efficiency.

---

**✨ Key Features**

* **🎯 Intelligent Product Matching**  
   Automatically maps client product inquiries to relevant Pure Storage offerings using a curated recommendation database.  
* **🤖 AI-Powered Email Generation**  
   Generates personalized, conversion-focused emails tailored to each prospect’s needs and engagement context.  
* **⚡ Real-Time CRM Synchronization**  
   Updates Salesforce lead records instantly with recommendations, AI-generated email content, and timestamps.  
* **🔍 Smart Lead Filtering**  
   Processes only leads with a “New” status to ensure optimal resource allocation and efficiency.  
* **📊 Batch Processing**  
   Handles multiple leads simultaneously with optimized performance and built-in error handling.  
* **🛡️ Data Validation**  
   Enforces data quality through normalization, field validation, and consistency checks across all workflow stages.

---

**🔄 Workflow Architecture**

**Data Ingestion Layer**

1. **Salesforce Lead Retrieval** – Fetches active leads including key fields (Client ID, Company, Name, Email, Product Inquiry, Status).  
2. **Google Sheets Integration** – Retrieves the approved Pure Storage product recommendation matrix from a centralized configuration.  
3. **Data Merge & Normalization** – Combines and harmonizes datasets using intelligent field-mapping logic.

**Intelligence Layer**

4. **Product Mapping Engine** – Custom JavaScript logic builds product associations and matches client inquiries to approved offerings.  
5. **Lead Status Filtering** – Ensures only leads with “New” status proceed to recommendation generation.  
6. **Batch Orchestration** – Manages sequential processing with rate limiting for controlled throughput.

**AI Generation Layer**

7. **LLM Recommendation Engine** – Utilizes the **Perplexity AI model** to generate 4–6 tailored product recommendations per client context.  
8. **Email Composition** – Crafts 120–170 word personalized emails with subject lines, value propositions, and clear CTAs.  
9. **Response Parsing** – Extracts structured JSON with fallback handling and standardized formatting (IST timezone, proper capitalization).

**Output Layer**

10. **Salesforce Update** – Writes recommendations, generated emails, and timestamps back to lead records, updating the status to “Contacted.”

---

**🎯 Business Impact**

* **⏱️ Time Savings** – Reduces lead response time from hours to minutes by automating research and outreach.  
* **🎨 Personalization at Scale** – Delivers custom recommendations for every lead without sacrificing quality.  
* **📈 Conversion Optimization** – AI-generated emails follow proven sales enablement strategies for higher engagement.  
* **🔒 Compliance & Accuracy** – Recommendations strictly follow the approved product catalog, ensuring brand consistency.  
* **📊 CRM Data Enrichment** – Maintains complete audit trails with timestamps and interaction history for analytics.

---

**🛠️ Technical Highlights**

* **Modular Design** – 12 specialized nodes following n8n best practices for maintainability and clarity.  
* **Error Resilience** – Multiple validation checkpoints and graceful fallback mechanisms throughout the pipeline.  
* **Secure Credential Management** – All API keys and credentials are stored securely via n8n’s encrypted credential system.  
* **Performance Optimization** – Efficient data structures and batch processing minimize API calls and execution time.  
* **IST Timezone Support** – Custom timestamp formatting for regional accuracy (+05:30 offset).

---

**📦 Integration Stack**

| Component | Platform |
| :---- | :---- |
| **CRM** | Salesforce Sales Cloud |
| **Data Source** | Google Sheets (Recommendation Matrix) |
| **AI Model** | Perplexity AI (LLM) |
| **Automation Platform** | n8n v3.x |
| **Data Processing** | Custom JavaScript (ES6+) |

---
## Required credentials

To run this workflow in n8n, configure the following credentials in your n8n instance (do **not** commit secrets to GitHub):

1. **Google Sheets**
   - A Google API credential with access to the spreadsheet ID used in the nodes (e.g. `1y7jcrKOeskvI1Po_anS5U9QenZgf-e-oPzMiliPwZOw`). [file:1]
   - Required scopes typically include access to read and edit Google Sheets.
   - In n8n, create a **Google Sheets** credential and select it in all Google Sheets nodes.

2. **Perplexity (LLM)**
   - An API key for the Perplexity AI API (or the hosting you are using for the `perplexity` node). [file:1]
   - In n8n, create the corresponding **Perplexity** (or generic HTTP/LLM) credential and reference it in:
     - `Message a model`
     - `Message a model1`
     - `Message a model2` [file:1]

3. **Salesforce**
   - A Salesforce connected app / user with API access to the Lead object and the custom fields:
     - `Client_Id__c`
     - `Product_Inquiry__c`
     - `Product_Recommended__c`
     - `Email_Message__c`
     - `Timestamp__c` [file:1]
   - In n8n, configure a **Salesforce** credential (OAuth or username/password + token) and select it in:
     - `Get many leads`
     - `Update a lead` [file:1]

4. **(Optional) Timezone / Date & Time node**
   - If you choose to use a Date & Time node instead of code for timestamps, configure it to use the `Asia/Kolkata` timezone (IST) and map its output to the `Timestamp` or `Timestamp__c` field used in the code. [file:1]

## Environment variables and configuration

Depending on how you structure your n8n instance, you may want to keep sensitive values as environment variables and reference them in credentials or nodes:

- `PERPLEXITY_API_KEY` – API key for the Perplexity node.
- `GOOGLE_SHEETS_CLIENT_ID`, `GOOGLE_SHEETS_CLIENT_SECRET`, etc. – values used by n8n’s Google Sheets OAuth credential.
- `SALESFORCE_CLIENT_ID`, `SALESFORCE_CLIENT_SECRET`, `SALESFORCE_USERNAME`, `SALESFORCE_PASSWORD`, `SALESFORCE_TOKEN` – for the Salesforce credential. [file:1]

Do **not** store any of these secrets directly inside the JSON workflow in your GitHub repository.

## Usage

1. Import the JSON file into your n8n instance via “Import from file”.
2. Configure:
   - Google Sheets credentials and update sheet IDs/ranges if your copies differ.
   - Perplexity credentials and, if needed, the model name and `response_format` JSON schema.
   - Salesforce credentials and confirm custom field API names. [file:1]
3. Run once using the manual trigger to validate:
   - Recommendation mapping.
   - LLM JSON parsing.
   - Write‑back to Sheets and Salesforce. [file:1]
4. Enable the schedule trigger to run the workflow automatically on your desired interval. [file:1]


**🚦 Workflow Execution**

1. **Trigger:** Manual or scheduled execution  
2. **Parallel Fetch:** Retrieve Salesforce leads and recommendation matrix from Google Sheets  
3. **Data Consolidation:** Merge and normalize datasets  
4. **Filtering:** Process only “New” status leads  
5. **Batch Processing:** Sequential handling with AI-based generation  
6. **CRM Update:** Write recommendations and email outputs back to Salesforce  
7. **Completion:** All leads updated with “Contacted” status

---

**📝 Output Data Structure**

| Field | Description |
| :---- | :---- |
| **Product\_Recommended\_\_c** | Comma-separated list of 4–6 recommended Pure Storage products |
| **Email\_Message\_\_c** | Complete email text (subject line \+ body) |
| **Timestamp\_\_c** | IST-formatted processing timestamp |
| **Status** | Updated to “Contacted” |

---

**🎓 Best Practices Implemented**

✅ **Data Validation** – Input checks at every stage  
 ✅ **Error Handling** – Graceful degradation with fallback logic  
 ✅ **Security First** – Encrypted credentials and least-privilege access  
 ✅ **Documentation** – Clear node naming and inline comments  
 ✅ **Scalability** – Batch processing supports high-volume lead flows  
 ✅ **Version Control** – Workflow versioning via JSON export  
 ✅ **Modular Architecture** – Reusable components with clear separation of concerns

---

**🔐 Security & Compliance**

* All credentials are managed securely through n8n’s encrypted storage system.  
* No hardcoded API keys or sensitive data exist within the workflow.  
* Salesforce API access follows strict least-privilege principles.  
* Data handling fully complies with applicable regional privacy regulations.



## Notes

- The workflow is designed to be **case‑insensitive** when matching recommended products to the approved list. [file:1]
- All LLM outputs are parsed defensively: the code handles string, array, and JSON cases and adds sensible defaults if the model returns malformed JSON. [file:1]
- Timestamps are generated or normalized in **IST (UTC+05:30)** and written as human‑readable strings like `YYYY‑MM‑DD HH:MM:SS IST (+05:30)`. [file:1]


 

