# gemini-n8n-auto-resume-screener
AI Resume Screener built with n8n and Google Gemini. Automates candidate evaluation, shortlisting, and email communication by assigning objective qualification scores based on job requirements. Addresses common n8n data-pathing and connection errors for high reliability.

##  Automated AI Resume Screening and Shortlisting with n8n

This workflow is designed to automate the initial, time-consuming phase of the candidate screening process. It uses Google Gemini AI for objective, skills-based analysis, and **n8n** to manage the entire workflow, from application intake to communication and data logging.

This system drastically reduces the manual effort required to review resumes, ensuring recruiters only focus on candidates who meet the configured qualification threshold.

###  The Problem Solved

The development of this workflow highlighted a common challenge in automation: **solving one simple error often generated multiple new ones** (e.g., connection errors, broken data paths, logic flaws). The final, stable workflow addresses these common pitfalls to provide a robust solution.

---

##  Key Features

* **Objective AI Analysis:** Leverages the Gemini model to compare candidate skills, experience, and education against specific job requirements defined in the `Workflow Configuration` node.
* **Structured Data Output:** Uses a structured output parser (Code Node) to reliably extract key data points from the AI response, including a numerical **Qualification Score (1-10)**, Strengths, and Weaknesses.
* **Conditional Decision Logic:** An IF node (`Check Qualification Score`) automatically routes candidates:
    * **True Branch:** Score meets threshold $\rightarrow$ Shortlisted Process.
    * **False Branch:** Score is below threshold $\rightarrow$ Rejected Process.
* **Automated Data Logging:** Appends detailed candidate and AI analysis data to separate Google Sheet tabs (**Shortlisted** and **Rejected**) for centralized tracking.
* **Instant Communication:** Sends automated, customized emails (via Gmail) to the candidate based on the outcome (acceptance or rejection). **Emails are placed directly in the correct conditional branches** to prevent the common error of sending duplicate or incorrect emails.

---

##  Requirements

Before running this workflow, ensure you have the following configured:

1.  **n8n Instance:** Running locally or hosted.
2.  **Google Gemini API Key:** Required for the `AI Resume Analyzer` node.
3.  **Google Sheets Credential:** Authorized for both Sheets nodes (Shortlisted/Rejected).
4.  **Gmail Credential:** Authorized for the 'Send a message' nodes.

### Setup Instructions (The Critical Steps)

1.  **Google Sheet Setup:** Create a new Google Sheet document with two tabs named exactly: `Shortlisted` and `Rejected`. Ensure the header rows match the required columns.

2.  **Configuration:** Open the workflow in your n8n editor.

    * **CRITICAL FIX (Connection):** Update the **Document ID** in **BOTH** the `Add to Shortlisted Candidates` and `Add to Rejected Candidates` nodes with the actual ID from your Google Sheet's URL. *(The workflow will not execute without this.)*
    * **CRITICAL FIX (Rejected Node):** In the `Add to Rejected Candidates` node, change the **Operation** from "Append or Update Row" to **`Append`** to prevent the "Column to Match On" error.
    * **CRITICAL FIX (Data Paths):** Ensure the expressions for Name, Email, and Phone in all downstream nodes (Sheets and Gmail) point **reliably** to the source: `{{ $node['Resume Upload Form'].json[0]['Field Name'] }}`.
    * Configure the `Workflow Configuration` node with the current `jobTitle`, `requiredSkills`, and `qualificationThreshold`.

3.  **Deploy:** Activate the workflow and test by submitting a resume via the initial trigger form.
