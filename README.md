# chatbot-n8n

An n8n workflow that handles user message intake, document retrieval, AI-driven responses, and human escalation, with full logging.

## Key Features

* **Webhook Receiver:** Accepts POSTed messages.
* **Escalation Logic:** Detects keywords (e.g., "human") to route to a live agent via email.
* **Document Search & Conversion:** Queries a specified Google Drive folder, fetches top DOCX/PDF files, converts them to text (via Google Docs & PDF.co).
* **AI Response:** Combines user query and document content to prompt Google Gemini (PaLM) for a context-rich reply.
* **Logging:** Appends all interactions and escalations to Google Sheets for auditing.

## Prerequisites

* **n8n** instance
* **Google OAuth2 Credentials** for Drive, Sheets, Docs
* **Gmail OAuth2 Credentials** for sending emails
* **Google PaLM API Key** for Gemini node
* **PDF.co API Key** for PDF-to-text conversions
* A **Drive Folder ID** and **Google Sheet IDs** for logs

## Setup

1. **Import Workflow:**

   * Upload `My_workflow-7.json` in n8n.
2. **Attach Credentials:**

   * Link Google and Gmail credentials to respective nodes.
   * Enter PaLM and PDF.co API keys.
3. **Configure IDs:**

   * Set your Drive folder ID in the search node.
   * Set your logging sheet IDs in the append nodes.
4. **Activate & Test:**

   * Enable the workflow.
   * Note your webhook URL and send a test message.

## How It Works (High-Level)

1. **Receive & Normalize:** Webhook node takes in `{ userId, sessionId, messageText }`.
2. **Intent Check:** IF node branches on "human" or related terms.

   * **Yes:** Send notification email, log escalation, and reply with a human-handoff message.
   * **No:** Proceed to file search.
3. **Drive Search:** List files, dedupe, and pick the top result.
4. **Convert & Aggregate:** Convert DOCX to Docs, PDF to text, then merge content.
5. **AI Query:** Build prompt combining user text + document text, call Gemini, and capture the answer.
6. **Log & Respond:** Record the exchange in Sheets and return the AI-generated message.

## Usage Example

```bash
curl -X POST https://<n8n-domain>/webhook/receiveMessage \
  -H "Content-Type: application/json" \
  -d '{"userId":"123","sessionId":"abc","messageText":"Find policy docs"}'
```

**Response:**

```json
{ "message": "Here is the summary of the policy documents: ..." }
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for the full text:

```text
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
