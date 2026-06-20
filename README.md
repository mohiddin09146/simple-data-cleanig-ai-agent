# simple-data-cleanig-ai-agent

A simple, automated n8n workflow that fetches raw data from a Google Sheet, cleans it using custom JavaScript, and emails the finalized dataset to your inbox.

📊 Workflow Overview
This workflow automates the repetitive task of data preprocessing. 
It connects to a Google Sheets datasource, standardizes formatting errors, validates data types, and delivers a clean CSV report directly to you.

📋 Data Schema

The workflow processes rows containing the following columns:
Full Name: Raw customer or user names.
Email: Contact email addresses.Date:
Timestamps or submission dates.
Amount: Financial figures or transaction values.
Message: Text feedback or notes.

🚀 Features

.📥 Automated Retrieval: Pulls new or existing rows from a designated Google Sheet.
.💻 JavaScript Data Cleaning:Trims whitespace from names and messages.
. Converts email addresses to lowercase for consistency.Standardizes date formats.Sanitizes and formats currency/amounts into clean numbers.
📧 Email Delivery: Standardizes the cleaned data and sends a completion confirmation with the dataset to your specified email.

🛠️ Prerequisites

Before importing this workflow, ensure you have the following:An active n8n instance.
A Google Workspace account with 🟢 Google Sheets API access enabled.
An Email/SMTP service configured in n8n (e.g., 🔴 Gmail, Outlook, or Mailgun).

📝 Setup Instructions

1. 🟢 Google Sheets PreparationCreate a Google Sheet with the headers: Full Name, Email, Date, Amount, and Message.
2. Add your raw data into the rows below.
3. Note the Spreadsheet ID from the URL and the Sheet Name.
  
 🔀 Import the Workflow to n8n
 1.Copy the JSON code from the workflow.json file in this repository.
2. Open your n8n editor UI.
3.Press Ctrl + V (or Cmd + V on Mac) to paste the workflow directly onto the canvas.

3. 🔑 Configure Node Credentials
. 🟢 Google Sheets Node: Click the node and link your Google Service Account or OAuth2 credentials. Select your Spreadsheet ID.
📧 Email Node: Click the node and set up your SMTP credentials. Update the To Email field with your destination email address.

4. 🟡 JavaScript Cleaning Logic
. The Code node uses a JavaScript snippet to map and clean the incoming data. It runs a loop similar to this:
const data = $json;

// 1. Fixed the key to 'full nmae' and added optional chaining (?.)
const cleanFull = (data['full nmae'] || "").trim().replace(/\s+/g, ' ');
const parts = cleanFull.split(" ");
const frist = parts[0] || "";
const last = parts[1] || "";

// 2. Added optional chaining to prevent future crashes if fields are missing
let email = (data.email || "").trim().toLowerCase();

const rawAmount = data.amount;
const amountStr = String(rawAmount).trim().replace(/[^0-9.]/g, "");
const amount = parseFloat(amountStr) || 0;

let rawDate = data.date;
let message = (data.message || "").trim().replace(/\s+/g, ' ');

// Transformation function
function cap(s) {
  return s.charAt(0).toUpperCase() + s.slice(1).toLowerCase();
}

const fristname = cap(frist);
const lastname = cap(last);
const d = new Date(rawDate);
const niceDate = d.toDateString();

const subject = `Quote Request from ${fristname} - Amount ${amount}`;
const cleanMsg = message.charAt(0).toUpperCase() + message.slice(1);

return { fristname, lastname, email, amount, niceDate, subject, cleanMsg };





