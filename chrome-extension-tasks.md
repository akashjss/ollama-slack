**Component: Chrome Extension**

1.  **Extension Structure:**
    *   In the `chrome-extension` folder, `@AI Generate` a basic `manifest.json` (V3) file.
    *   `@AI Edit` `manifest.json`:
        *   Add `name`, `version`, `description`.
        *   Add `permissions` for `scripting`, `activeTab`.
        *   Add `host_permissions` for `https://app.slack.com/*` and your backend URL (e.g., `http://localhost:5000/*`). (Based on FR1.0, FR1.2)
        *   Define `content_scripts` to inject `content.js` into `https://app.slack.com/*`. (Based on FR1.0)
    *   In `chrome-extension`, create an empty `content.js` file.
2.  **Content Script - UI Injection:**
    *   In `content.js`, write JavaScript code to run when the script is injected.
    *   `@AI Ask`: "How can I reliably find the main message div elements in the current Slack web page DOM using JavaScript?" Use this to guide your selectors.
    *   Implement logic to find message containers or thread headers (FR1.0). *Manual inspection and iterative refinement of selectors will be needed here.*
    *   `@AI Generate` a JavaScript function `createActionButton(text, action)` that returns a button DOM element with event listeners. (Based on FR1.0)
    *   Implement logic to inject `createActionButton` instances ("Summarize", "Draft Reply") near the identified Slack UI elements. (Based on FR1.0, US about buttons)
3.  **Content Script - Event Handling & Context:**
    *   In `content.js`, add event listeners to the injected buttons (already part of `createActionButton`).
    *   In the button click handler, implement logic to traverse the DOM *upwards* from the clicked button to find the nearest message or thread container element.
    *   `@AI Ask`: "How can I extract the Slack channel ID, thread timestamp, or message timestamp from the DOM elements or the URL on the Slack web page?" Use this to guide your context extraction logic (FR1.1). *Again, fragile, requires manual DOM inspection.*
    *   Identify the `action` based on the button clicked (`summarize`, `draft_reply`).
4.  **Content Script - Trigger Backend Request:**
    *   In `content.js`, `@AI Generate` an asynchronous JavaScript function `sendToBackend(context, action)`.
    *   `@AI Edit` `sendToBackend`:
        *   Use `fetch` to make a POST request to your backend URL (e.g., `http://localhost:5000/process_slack`). (Based on FR1.2)
        *   Set `Content-Type` header to `application/json`.
        *   Set the request body using `JSON.stringify({ context: context, action: action })`. (Based on FR1.2)
        *   Handle potential network errors and non-200 HTTP responses from the backend. (Based on FR1.5, FR2.8)
5.  **Content Script - Response Handling & Display:**
    *   In `content.js`, `@AI Generate` JavaScript code to create a modal or sidebar element to display the response. Make it initially hidden. (Based on FR1.3, US about display)
    *   In the `sendToBackend`'s `.then()` block (after receiving a successful response), parse the JSON response body to get the text. (Based on FR1.3)
    *   Update the display element with the received text and make it visible. Ensure the text is selectable (NFR Usability).
6.  **Content Script - Loading/Error States:**
    *   In `content.js`, update the button click handler: Before calling `sendToBackend`, disable the button and show a loading indicator near it or in the display area. (Based on FR1.4)
    *   In the `.then()` and `.catch()` blocks of `sendToBackend`, remove the loading indicator and re-enable the button.
    *   If an error occurs, update the display element with an error message. (Based on FR1.5)
7.  **Testing (Chrome Extension):**
    *   Open Chrome, go to `chrome://extensions`, enable Developer Mode.
    *   Click "Load unpacked" and select your `chrome-extension` folder.
    *   Navigate to a Slack web page (`app.slack.com`).
    *   Verify buttons appear in the Slack UI (FR1.0). *Iterate on selectors if they don't.*
    *   Open the Chrome Developer Tools (F12), go to the "Console" and "Network" tabs.
    *   Click your injected buttons. Verify requests are sent to `localhost:5000` in the Network tab (FR1.2).
    *   Check the Console for any JavaScript errors.
    *   If the backend is running and returns a response, verify the response is displayed correctly in your custom UI element (FR1.3).
    *   Test error states (backend stopped, backend returns error response).
