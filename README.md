# **Next Best Action (NBA) Engine for Twitter Customer Support**

This repository hosts a complete end-to-end **Next Best Action Engine** designed to enhance customer service on Twitter. It reads real customer support interactions, analyzes user behavior, and generates intelligent response strategies using an LLM-based decision engine.\n
- [Problem Statement](https://riverline.notion.site/ml-assignment)

---

## **📦 Dataset**

I used the [Twitter Customer Support Dataset (TWSC)](https://www.kaggle.com/datasets/thoughtvector/customer-support-on-twitter/data) from Kaggle. It contains:
- Tweet threads between customers and brands
- Timestamps and message directions
- Metadata useful for conversation flow extraction

### **🗂️ Place raw dataset:**
Download the `twcs.csv` file and place it in:

```

data/raw/twcs.csv

```

This is your starting point for the ingestion pipeline.

---

## **🚀 Project Structure**

```

Riverline\_NBA\_System/
│
├── data/
│   ├── raw/                 # Raw CSV file (twcs.csv or new\_tweets.csv)
│   └── processed/           # SQLite DB containing normalized tweets
│
├── src/
│   ├── components/          # Modular logic for ingestion, NLP tagging, clustering
│   ├── models/              # Model loading: SBERT, ZSC, Sentiment, LLM
│   ├── pipeline/            # Modular pipelines for ingestion, processing, NBA engine
│   ├── utils/
│
├── notebooks/              # 3 notebooks to walk through dev process
│   ├── 1\_ingestion\_pipeline\_dev.ipynb
│   ├── 2\_behavior\_analysis\_dev.ipynb
│   └── 3\_nba\_engine\_dev.ipynb
│
├── outputs/
│   ├── json/               # Parsed + tagged + NBA updated conversations
│   ├── csv/                # Cluster tables, chat logs, NBA outputs
│
├── main.py                # FastAPI app for submitting new tweets
├── config.yaml            # Config file for paths and DB settings
└── requirements.txt       # Python dependencies

```

---

## **🧩 How It Works**

### **💡 Core Idea**

This system simulates a smart **Customer Support Agent** that understands:
- Customer sentiment & urgency
- Issue type and resolution flow
- Ideal channel & timing for next action

It then **generates actionable replies** (DM, email, or phone) using LLMs with structured reasoning.

---

### **⚙️ Modular Pipeline Architecture**

I split the pipeline into 3 clear stages:

#### 1️⃣ Ingestion Pipeline
- Reads new tweets from CSV
- Filters out resolved customers (if any)
- Normalizes them into structured tables (SQLite)
- Output: `new_raw_tweets` and `new_interaction_table`

#### 2️⃣ User Behavior Analysis
- Groups tweets into conversations
- Tags fine-grained issues using Zero-Shot Classification (ZSC)
- Tags sentiment, detects trajectory (worsening/improving)
- Clusters conversation flows using `BERTopic`

#### 3️⃣ NBA Engine
- Loads conversation summary + flow
- Prompts a **LLM (LLaMA3 via Ollama)** to reason:
  - Best channel (DM / Email / Phone)
  - Best time to respond (UTC)
  - Best response message
  - Whether issue is resolved
- Stores results in:
  - `nba_actions_log.csv`
  - Appends agent reply to raw tweets → re-ingested

---

## **🧪 Notebooks (Highly Recommended)**

To understand the full pipeline logic and model building:

1. `notebooks/01_data_ingestion.ipynb`
2. `notebooks/02_user_behavior.ipynb`
3. `notebooks/3_nba_engine.ipynb`

These walk through the development process of each module.

---

## **🧪 How to Run**

### **🔧 1. Install Requirements**
```
pip install -r requirements.txt
````

Make sure to also install and run **Ollama** locally for the LLaMA model:

```
ollama run llama3
```

### **🧵 2. Submit a Tweet via FastAPI**

Launch the FastAPI server:

```
uvicorn main:app --reload
```

Visit [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) to access Swagger UI and test:

#### **Example Request:**

```
POST /submit_tweet/
{
  "author_id": "user123",
  "text": "I can't reset my password!",
  "in_response_to_tweet_id": "NA",
  "response_tweet_id": "NA"
}
```

This will:

1. Append the tweet to `new_raw_tweets.csv`
2. Trigger all 3 pipelines in order
3. Inject agent's NBA reply and re-normalize tables

---

## **🛠️ Key Models & Techniques Used**

| Component                | Model / Tool                     |
| ------------------------ | -------------------------------- |
| Sentiment Tagging        | FinBERT                          |
| Zero-Shot Classification | BART-MNLI                        |
| Embedding Model          | SBERT (E5)                       |
| Flow Clustering          | BERTopic                         |
| LLM for NBA Decisions    | LLaMA 3 (via Ollama)             |
| Structured Reasoning     | LangChain StructuredOutputParser |

---

## **🔮 Future Development**

### ✅ Next Goals:

* ⚙️ **MLOps Level 1** Automation using [**Airflow DAGs**](https://airflow.apache.org/) via **Astro CLI**
* 👤 Add **MTBI-based Persona Tagging** to personalize responses further
* 🔁 Automate retraining or rule updates based on feedback loop

