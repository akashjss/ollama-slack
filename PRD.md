Okay, here is a draft Product Requirements Document (PRD) for the application you've described.

This PRD outlines the "what" and "why" of the application from a user and functional perspective, based on the agreed-upon architecture (Slack UI -> Chrome Extension -> Python Backend -> Ollama).

---

## Product Requirements Document: Slack-Ollama Assistant

**Version:** 1.0
**Date:** 2023-10-27
**Author:** [Your Name/Team Name]

---

**1. Introduction**

This document outlines the requirements for a personal assistant application that integrates with Slack and a local Ollama Large Language Model (LLM) backend. The goal is to allow users to leverage the power of LLMs directly within their Slack workflow for tasks such as summarizing conversations or drafting replies, while keeping the processing local and private.

The application consists of three main components: a Chrome browser extension, a local Python backend service, and a local Ollama instance with a loaded model.

**2. Goals and Objectives**

*   Enable users to easily initiate LLM-based actions (like summarization or reply drafting) from within the Slack web interface.
*   Provide these capabilities without sending sensitive Slack conversation data to external, cloud-based LLM providers.
*   Improve user productivity in Slack by offering quick access to AI assistance based on conversation context.
*   Create a system that is relatively easy for a user comfortable with running local services to set up and use.

**3. Target Audience**

*   Individuals who use Slack regularly and wish to experiment with or utilize local LLMs for productivity.
*   Developers, researchers, or power users comfortable with installing and running local software (Python, Ollama).

**4. Scope**

**4.1. In Scope**

*   **Chrome Extension:**
    *   Inject UI elements (buttons) into specific Slack web UI areas (e.g., near messages, within threads).
    *   Identify the relevant Slack context (e.g., channel ID, thread timestamp, message timestamp) when a UI element is activated.
    *   Send a trigger request, including the context identifiers, to the local Python backend via HTTP.
    *   Receive and display the processed text response from the Python backend within the Chrome browser (e.g., in a popup, modal, or injected div).
*   **Python Backend:**
    *   Run as a local service (e.g., a simple Flask or FastAPI application).
    *   Expose an HTTP endpoint to receive requests from the Chrome Extension.
    *   Authenticate with the Slack API using a user-provided token.
    *   Use the Slack API to fetch historical messages and thread replies based on the context IDs received from the extension.
    *   Format the fetched Slack conversation data into a suitable text prompt for the LLM.
    *   Make an HTTP request to the local Ollama API (assuming it's running on `localhost:11434` by default).
    *   Send the prompt to Ollama and receive its text response.
    *   Basic processing/formatting of Ollama's raw output.
    *   Return the processed output as the HTTP response back to the Chrome Extension.
*   **Integrations:**
    *   Communication with Slack Web API (read-only permissions for relevant conversations).
    *   Communication with Ollama HTTP API.
*   **Use Cases (Initial):**
    *   Summarize a Slack thread when triggered by the user.
    *   Draft a reply to a specific message within a thread, considering the thread context.

**4.2. Out of Scope (for Version 1.0)**

*   Automatic, non-user-triggered processing of messages.
*   Writing or posting messages directly to Slack via the Slack API (user will manually copy/paste from the extension UI).
*   Handling complex Slack message elements (files, blocks, complex formatting beyond basic text).
*   Support for real-time Slack events (via Webhooks or Socket Mode).
*   User management or multi-user support (designed for a single user's local setup).
*   Advanced Ollama features (e.g., function calling, streaming responses handling beyond basic text).
*   Cross-browser compatibility (Chrome only).
*   Sophisticated error handling and reporting beyond basic messages.
*   Packaging or installers for the Python backend.

**5. User Stories**

*   As a Slack user, I want to see a button on a Slack thread that I can click to get a summary generated by my local AI.
*   As a Slack user, when I read a message, I want to be able to trigger my local AI to draft a reply based on that message and the preceding conversation in the thread.
*   As a Slack user, I want the AI-generated summary or draft to appear in my browser window so I can review and edit it before posting it myself.
*   As a user setting up the application, I need a way to configure my Slack API token and potentially the Ollama endpoint in the Python backend.
*   As a user, if the backend or Ollama is not running, I want the extension to indicate that the request failed.

**6. Functional Requirements**

*   **FR1.0 - Chrome Extension UI Injection:** The extension MUST inject a clearly identifiable button or UI element into the Slack web interface for individual messages and threads.
*   **FR1.1 - Context Identification:** The extension MUST be able to extract the necessary Slack identifiers (channel ID, thread timestamp, message timestamp) when a trigger button is clicked.
*   **FR1.2 - Backend Trigger:** The extension MUST send an HTTP POST request to a configurable local endpoint (defaulting to `http://localhost:<port>`) exposed by the Python backend upon trigger activation. The request payload MUST include the Slack context identifiers and the requested action (e.g., "summarize", "draft_reply").
*   **FR1.3 - Response Display:** The extension MUST receive the text response from the Python backend and display it in a dedicated area within the browser window (e.g., an overlay, side panel, or popup associated with the extension icon).
*   **FR1.4 - Loading State:** The extension UI MUST indicate a loading state while waiting for the response from the backend.
*   **FR1.5 - Error Display (Extension):** The extension MUST display an error message if the request to the backend fails (e.g., backend not running, network error).
*   **FR2.0 - Backend HTTP Server:** The Python backend MUST run a simple HTTP server listening on a configurable local port.
*   **FR2.1 - Request Handling:** The backend MUST receive and parse incoming HTTP requests from the Chrome extension, extracting the context identifiers and requested action.
*   **FR2.2 - Slack API Authentication:** The backend MUST authenticate with the Slack API using a user-provided OAuth token (ideally configured via environment variable or config file).
*   **FR2.3 - Fetch Slack Data:** Based on the context IDs and action, the backend MUST use the `slack_sdk` to call the appropriate Slack API methods (e.g., `conversations.history`, `conversations.replies`) to fetch message data.
*   **FR2.4 - Prompt Formatting:** The backend MUST format the fetched Slack message data into a text prompt suitable for an LLM running on Ollama, including instructions based on the requested action (summarize/draft reply).
*   **FR2.5 - Ollama API Call:** The backend MUST make an HTTP POST request to the local Ollama API endpoint (defaulting to `http://localhost:11434/api/generate`).
*   **FR2.6 - Ollama Response Processing:** The backend MUST receive and parse the JSON response from the Ollama API, extracting the generated text.
*   **FR2.7 - Response Return:** The backend MUST return the processed Ollama text response to the Chrome extension as the body of the HTTP response.
*   **FR2.8 - Error Handling (Backend):** The backend MUST handle errors during Slack API calls, Ollama API calls (e.g., Ollama not reachable), and data processing, returning an appropriate error indicator or message to the Chrome extension.
*   **FR3.0 - Configuration:** The Python backend MUST provide a simple mechanism for the user to configure the Slack API token and the Ollama endpoint URL (e.g., environment variables, command-line arguments).
*   **FR3.1 - Ollama Compatibility:** The backend MUST be compatible with the standard Ollama HTTP API `/api/generate` endpoint.
*   **FR3.2 - Slack API Permissions:** The user must be instructed on obtaining a Slack API token with the minimum necessary read permissions (e.g., `channels:history`, `groups:history`, `mpim:history`, `im:history`, `channels:read`, `groups:read`, `users:read`, `team:read`).

**7. Non-Functional Requirements**

*   **Performance:** The round trip from triggering the extension to displaying the response should be as fast as possible, primarily limited by the Ollama model's generation speed and local network latency. The Python backend should add minimal overhead.
*   **Reliability:** The system should be resilient to temporary network issues or the Ollama service being unavailable, providing informative error messages to the user via the extension.
*   **Security:** The Slack API token MUST NOT be stored in the Chrome extension code. It MUST be stored securely on the local machine where the Python backend runs (e.g., environment variable). Communication on `localhost` is generally considered safe from external threats but users should be aware if exposing ports.
*   **Usability:** The Chrome extension's UI elements should be unobtrusive but easily discoverable within the Slack interface. The response display should be clear and easy to copy text from.
*   **Maintainability:** The Python backend code should be modular to allow for easier addition of new features (e.g., different LLM backends, more complex prompt formatting, additional Slack API interactions).
*   **Compatibility:** The Chrome extension should be compatible with recent versions of the Chrome browser and the current Slack web interface layout (acknowledging the fragility of UI injection).

**8. Open Questions**

*   What is the exact port the Python backend will listen on by default? How will conflicts be handled?
*   What is the optimal prompt structure for different actions (summarize, draft reply) for common LLMs compatible with Ollama? This may require experimentation.
*   How will the user start and manage the Python backend process (e.g., always-on service, manual start)?
*   What specific Slack conversation types (public channels, private channels, DMs, group DMs) need to be supported initially?
*   How much historical context should be fetched from Slack for summarization/reply drafting? (e.g., last N messages, all messages in thread?)

**9. Future Considerations**

*   Adding functionality to post AI-generated responses *directly* to Slack (requires `chat:write` permissions and careful handling of user intent/control).
*   Supporting more complex Slack message features (attachments, blocks, mentions).
*   Implementing real-time processing triggered by new messages (requires backend to listen to Slack events via Socket Mode or Webhooks).
*   Adding support for other local LLM backends or cloud APIs.
*   Providing more advanced configuration options via a simple web UI for the backend or the Chrome extension's options page.
*   Adding more refined prompt engineering features or customizable prompts.
*   Adding support for other browsers (Firefox, Edge).

---