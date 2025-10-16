**Twitter Agent (n8n \+ Google Sheets \+ Apify)**

---

This workflow automates the process of extracting, filtering, and analyzing Twitter post performance using n8n, Apify, and Google Sheets. It identifies high-engagement tweets based on likes, retweets, and replies, then stores the top-performing results for reporting, analytics, or trend summarization.

---

**🔁 Workflow Overview**

**Purpose:**  
 To automatically identify and save the most engaging tweets from a dataset, helping analytics teams and clients focus on the most impactful content.

**Core Capabilities:**

* Fetches tweet data scraped via Apify.  
* Reads tweet metrics from a Google Sheet dataset.  
* Calculates custom engagement scores.  
* Filters and selects the top 50% of tweets based on engagement.  
* Appends high-potential tweets into a destination Google Sheet for insights or dashboards.

---

**⚙️ Workflow Structure**

| Step | Node | Action | Description |
| :---- | :---- | :---- | :---- |
| **1** | **Manual Trigger** | **When clicking 'Execute workflow'** | **Starts the process on demand from n8n’s UI.** |
| **2** | **Run an Actor (Apify)** | **Executes selected Apify actor** | **Fetches or refreshes dataset from Apify (e.g., Tweet Scraper).** |
| **3** | **Get Row(s) in Sheet** | **Reads from source Google Sheet** | **Pulls the scraped tweet data using the Google Sheets integration.** |
| **4** | **Code (JavaScript)** | **Calculates an engagement score** | **Uses likes, retweets, and reply counts to derive a score and extract the top 50%.** |
| **5** | **Append Row in Sheet** | **Writes to results Google Sheet** | **Appends filtered tweets (with highest engagement scores) to a destination Google Sheet for further analysis.** |

---

**🧮 Engagement Scoring Logic**

The scoring algorithm used in the Code node ranks tweets using a weighted formula:

Engagement Score=(Likes×0.6)+(Retweets×0.3)+(Replies×0.1)

Engagement Score=(Likes×0.6)+(Retweets×0.3)+(Replies×0.1)

* Tweets are sorted in descending order of this score.  
* Only the top 50% (configurable) are retained for storage.

This logic ensures that content with both volume and interaction quality ranks higher.

---

**📊 Data Flow Summary**

1. **Apify provides tweet dataset access.**  
2. **Google Sheets serves as a structured repository of raw data.**  
3. **JavaScript logic filters top tweets based on interaction metrics.**  
4. **The processed results are appended to a new sheet for team review and visualization.**

---

**💡 Key Benefits**

* Data-Driven Insights: Focus on the most impactful tweets driving engagement.  
* Automated Workflow: Reduces manual curation time.  
* Customizable Metrics: Easily adjust weight percentages or selection ratio.  
* Google Sheets Friendly: Enables seamless reporting, sharing, and live dashboards.

---

**📁 Integration Details**

* Apify: Used to run the tweet-scraping actor and manage dataset IDs.  
* Google Sheets (Source): Holds raw tweet data with metrics like likeCount, retweetCount, replyCount, etc.  
* Google Sheets (Destination): Stores filtered high-engagement tweets with computed “score” values.

 

 

