# Slack-Ollama Assistant

A powerful local AI assistant that integrates with Slack, allowing you to leverage the capabilities of local Large Language Models (LLMs) directly within your Slack workflow. This application enables you to summarize conversations and draft replies while keeping all processing local and private.

## 🌟 Features

- **Thread Summarization**: Get AI-generated summaries of Slack threads
- **Reply Drafting**: Generate contextual reply drafts based on conversation history
- **Local Processing**: All AI processing happens locally using Ollama
- **Privacy-Focused**: No data leaves your machine
- **Easy Integration**: Seamless integration with Slack's web interface

## 🏗️ Architecture

```
Slack Web UI → Chrome Extension → Python Backend → Ollama LLM
```

## 📋 Prerequisites

- Google Chrome browser
- Python 3.8 or higher
- [Ollama](https://ollama.ai/) installed and running locally
- Slack workspace access
- Slack API token with the following permissions:
  - `channels:history`
  - `groups:history`
  - `mpim:history`
  - `im:history`
  - `channels:read`
  - `groups:read`
  - `users:read`
  - `team:read`

## 🚀 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/slack-ollama-assistant.git
   cd slack-ollama-assistant
   ```

2. **Install Python dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   ```
   Edit `.env` and add your Slack API token:
   ```
   SLACK_API_TOKEN=xoxp-your-token-here
   ```

4. **Load the Chrome Extension**
   - Open Chrome and navigate to `chrome://extensions/`
   - Enable "Developer mode"
   - Click "Load unpacked"
   - Select the `extension` directory from this repository

5. **Start the Python backend**
   ```bash
   python backend/app.py
   ```

## 💻 Usage

1. Open Slack in your Chrome browser
2. Navigate to any thread or conversation
3. Look for the assistant buttons that appear next to messages
4. Click the appropriate button to:
   - Summarize the thread
   - Draft a reply

The AI-generated content will appear in a popup window, where you can review and copy it to use in Slack.

## 🔧 Configuration

### Backend Configuration
- Default port: 5000
- Ollama endpoint: http://localhost:11434
- Configuration can be modified in `.env` file

### Chrome Extension
- Backend URL can be configured in the extension settings
- Default: http://localhost:5000

## 🛠️ Development

### Project Structure
```
slack-ollama-assistant/
├── backend/           # Python backend service
├── extension/         # Chrome extension
├── .env.example      # Example environment variables
├── requirements.txt   # Python dependencies
└── README.md         # This file
```

### Running Tests
```bash
python -m pytest tests/
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⚠️ Known Limitations

- Currently supports Chrome browser only
- Requires manual copy/paste of AI-generated content
- Limited to basic text processing (no support for files, blocks, or complex formatting)
- Designed for single-user local setup

## 🔮 Future Plans

- Direct message posting to Slack
- Support for more complex Slack message features
- Real-time message processing
- Additional LLM backend support
- Cross-browser compatibility
- Web UI for configuration

## 🆘 Troubleshooting

### Common Issues

1. **Backend not responding**
   - Ensure the Python backend is running
   - Check if the port is available
   - Verify environment variables are set correctly

2. **Ollama connection issues**
   - Confirm Ollama is running locally
   - Check if the Ollama API is accessible at http://localhost:11434

3. **Extension not working**
   - Verify the extension is loaded in Chrome
   - Check the backend URL in extension settings
   - Ensure you're using the Slack web interface

## 📚 Additional Resources

- [Ollama Documentation](https://github.com/ollama/ollama)
- [Slack API Documentation](https://api.slack.com/)
- [Chrome Extension Development Guide](https://developer.chrome.com/docs/extensions/) 