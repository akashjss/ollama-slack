**Component: Python Backend Application**

1.  **Backend Skeleton:**
    *   In the `python-backend` folder, `@AI Generate` a `requirements.txt` file listing `Flask`, `requests`, and `slack_sdk`.
    *   Run `pip install -r requirements.txt` in your terminal.
    *   In `python-backend`, `@AI Generate` a basic Flask application structure in `app.py` that runs on `localhost:5000` and has one placeholder route `/process_slack`. (Based on FR2.0)
2.  **Configuration Loading:**
    *   In `app.py`, `@AI Edit` the code to add logic to load `SLACK_API_TOKEN` and `OLLAMA_URL` from environment variables using Python's `os` module. (Based on FR3.0, NFR Security)
3.  **Endpoint Implementation:**
    *   In `app.py`, `@AI Edit` the `/process_slack` route handler function:
        *   Add code to parse the incoming JSON request body for `channel_id`, `thread_ts`, `message_ts`, and `action`. Use Flask's `request.get_json()`. (Based on FR2.1)
        *   Add basic validation to check if the required fields are present.
4.  **Slack API Interaction:**
    *   In `app.py`, `@AI Generate` a Python function `fetch_slack_messages(channel_id, thread_ts=None, message_ts=None, token)` using `slack_sdk` to fetch messages.
    *   `@AI Edit` `fetch_slack_messages`:
        *   Initialize the `WebClient` with the provided token. (Based on FR2.2)
        *   Implement logic to call `conversations.replies` if `thread_ts` is provided, otherwise use `conversations.history` with `latest=message_ts` and `limit=1` or similar if `message_ts` is provided without `thread_ts`. Handle the case where only `channel_id` is given (fetch recent history). (Based on FR2.3)
        *   Include pagination handling if necessary (OQ about context depth).
        *   Add `try...except` blocks to catch `slack_sdk.errors.SlackApiError` and return a meaningful error or `None`. (Based on FR2.8)
    *   In the `/process_slack` route, add a call to `fetch_slack_messages` using the extracted context, handling potential errors.
5.  **Prompt Engineering:**
    *   In `app.py`, `@AI Generate` a Python function `format_messages_for_llm(messages_data, action)` that takes a list of Slack message dictionaries and the requested action (`summarize` or `draft_reply`).
    *   `@AI Edit` `format_messages_for_llm`:
        *   Loop through `messages_data` and extract relevant info (user, text, timestamp).
        *   Format this information into a single coherent string for the LLM prompt. Include instructions based on the `action`. (Based on FR2.4)
        *   `@AI Ask`: "What are some good prompt structures for summarizing a conversation?" or "How should I structure a prompt to draft a reply to the last message in a conversation?" Use the AI's suggestions to refine the formatting logic.
    *   In the `/process_slack` route, call `format_messages_for_llm` with the fetched messages.
6.  **Ollama API Interaction:**
    *   In `app.py`, `@AI Generate` a Python function `get_ollama_response(prompt, ollama_url)` using `requests`.
    *   `@AI Edit` `get_ollama_response`:
        *   Construct the payload for the Ollama `/api/generate` endpoint. (Based on FR2.5)
        *   Send the POST request to `ollama_url` + `/api/generate`.
        *   Parse the JSON response and extract the `response` field. (Based on FR2.6)
        *   Add `try...except` blocks for network errors (`requests.exceptions.RequestException`) and check the HTTP status code and response structure for errors. (Based on FR2.8)
        *   Return the extracted text or an error indicator/message.
    *   In the `/process_slack` route, call `get_ollama_response` with the formatted prompt, handling potential errors.
7.  **Response Return & Error Handling (Backend):**
    *   In the `/process_slack` route:
        *   If all steps (Slack fetch, Ollama call) are successful, return the Ollama response text as a JSON response with a 200 status code. (Based on FR2.7)
        *   If any step failed, return a JSON response with an appropriate error message and a non-200 status code (e.g., 500, 400). (Based on FR2.8)
8.  **Basic Running Script:**
    *   In `app.py`, add the standard `if __name__ == "__main__": app.run(...)` block to start the Flask development server. Allow configuring the port from environment variables or a default (OQ about running/port).
9.  **Testing (Backend):**
    *   Manually test the backend endpoint using `curl` or a tool like Thunder Client/Postman within Cursor.
    *   Set environment variables (`export SLACK_API_TOKEN='xoxb-...'`, `export OLLAMA_URL='http://localhost:11434'`).
    *   Run `python app.py`.
    *   Send sample requests simulating the extension's payload.
    *   Verify correct Slack API calls are made (check Slack API logs or add print statements).
    *   Verify correct prompts are sent to Ollama (check Ollama logs or add print statements).
    *   Verify correct responses are received and returned.
    *   Test error cases (Ollama stopped, invalid Slack token, invalid IDs in request).

---