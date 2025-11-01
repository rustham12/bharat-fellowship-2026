# Project Samarth: An AI Data Analyst Agent

This project is a fully conversational AI data analyst, "Samarth." You can ask complex questions about Indian agricultural and climate data in plain English. The agent understands your question, writes its own Python (`pandas`) code to analyze a set of raw CSV files, and gives you a complete, cited answer.

The entire application is built in a Streamlit web interface and is powered by **Google's Gemini 2.5 Pro** model, all running from a single Google Colab notebook.



---

## Key Features

* **Conversational Q&A:** Ask questions like "What was the top crop in Maharashtra?" instead of writing SQL or Python.
* **Autonomous Code Generation:** The agent (powered by Gemini 2.5 Pro) writes, executes, and self-corrects `pandas` code to find answers.
* **Multi-File Analysis:** The agent can reason about and use four different CSVs to answer complex, multi-part questions (e.g., "compare rainfall and crop production in 2015").
* **Web-Based UI:** A clean Streamlit app provides a simple chat interface for anyone to use.
* **API-Powered:** Uses the powerful Gemini 2.5 Pro model, which can easily handle complex, multi-step reasoning without the hardware limitations of local models.

---

## System Design: The Agent Loop

This project uses a "ReAct" (Reason-Act) agent framework. The AI's "brain" is contained in a master prompt that forces it to follow a specific, logical loop:

1.  **Thought:** The user's question is combined with the dataframe schemas and a set of **Critical Rules** (e.g., "all data is lowercase," "use `df_rainfall_ts` for time-series"). The Gemini model thinks about *how* to answer the question.
2.  **Action:** The model writes a Python code block to execute.
3.  **Execution:** The notebook safely executes this code and captures the `print()` output or any errors.
4.  **Observation:** This output is fed *back* to the LLM.
5.  **Loop:** The LLM reads the observation.
    * If the code failed, it thinks about how to *fix it* and writes new code.
    * If the code succeeded, it either writes *more* code to get the next piece of information or provides the final answer.

---

## How to Run this Project (in Google Colab)

This project is designed to run in a free Google Colab environment (no GPU required).

### 1. Prerequisites
* A Google Colab account.
* A **Google API Key** (from Google AI Studio) with access to the Gemini 2.5 Pro model.
* An **Ngrok Auth Token** (to create a public URL for the app).

### 2. Setup
1.  Open the notebook in Google Colab.
2.  In the Colab sidebar, click the **🔑 (Secrets)** icon and add your tokens:
    * `GOOGLE_API_KEY` = Your Google AI Studio API key
    * `NGROK_AUTH_TOKEN` = Your Ngrok auth token
3.  In the Colab file explorer, **upload the four data CSVs** to the `/content/` directory.

### 3. Running the App
1.  **Run Cell 1 (`!pip install...`):** Installs all required libraries (`langchain-google-genai`, `streamlit`, `pandas`, etc.).
2.  **Run Cell 2 (Imports & Keys):** Loads the `GOOGLE_API_KEY` from secrets into the environment.
3.  **Run Cell 3 (Data Loading):** Runs the `load_all_data` function. This is a critical step that cleans and normalizes all data (e.g., converts all `State`, `District`, and `Crop` names to lowercase).
4.  **Run Cell 4 (Agent Setup):** This cell initializes the `ChatGoogleGenerativeAI` model (Gemini 2.5 Pro) and defines the `run_agent` function with its master prompt.
5.  **Run Cell 5 (Agent Test):** (Optional) You can run this cell to test the agent directly inside the notebook.
6.  **Run Cell 6 (`%%writefile app.py`):** This saves your Streamlit application code.
7.  **Run Cell 7 (Ngrok Launcher):** This starts the Streamlit app in the background and then creates a public `ngrok` URL.

Click the public URL output by the final cell to open and use your AI data analyst!

---

## Data Sources
The agent is connected to the following four datasets (which must be in the `/content/` directory):
* `APY.csv`: State, District, Crop, Year, Season, Area, and Production data.
* `sub-division_rainfall_act_dep_1901-2015.csv`: Monthly and annual rainfall time-series data for meteorological sub-divisions.
* `district _rainfall.csv`: Average (normal) rainfall for each district.
* `TEMP_ANNUAL_SEASONAL_MEAN.csv`: All-India annual mean temperature time-series.

---

## Tech Stack
* **AI Agent:** `LangChain`
* **LLM:** `Google Gemini 2.5 Pro` (via `langchain-google-genai`)
* **Data Analysis:** `pandas`
* **Web App:** `Streamlit`
* **Hosting:** `Google Colab` + `ngrok`
