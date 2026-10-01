# Gemini AI Chatbot

A lightweight AI chatbot built with **Python**, **Flask**, and the **Google Gen AI SDK**. Enter a prompt in a browser, send it to Gemini, and read the generated response through a clean, responsive interface.

This project demonstrates how a Flask application connects an HTML form to a generative AI service, with separate modules for routing, model interaction, and configuration.

## Features

- **AI-generated responses** through the Gemini API.
- **Responsive interface** built with HTML and CSS.
- **Server-rendered pages** using Flask and Jinja2.
- **Environment-based credentials** to keep the API key out of the source code.
- **Readable response display** that preserves line breaks and wraps long text.
- **Minimal architecture** with no database or frontend build step.

> Each prompt is processed independently. The current implementation does not retain conversation history or provide multi-turn memory.

## Technology Stack

| Component | Technology | Purpose |
| --- | --- | --- |
| Backend | Python, Flask | Request handling and application routes |
| AI integration | Google Gen AI SDK (`google-genai`) | Sending prompts to Gemini |
| Templates | Jinja2 | Rendering the home and response pages |
| Frontend | HTML5, CSS3 | Form layout, styling, and responsive design |
| Configuration | Python `os.environ` | Reading the API key from the environment |

Dependency versions are pinned in `requirements.txt`.

## Project Structure

Arrange the files as follows before running the application. Flask expects both HTML files inside a folder named `templates` beside `app.py`.

| Path | Description |
| --- | --- |
| `app.py` | Flask application and web routes |
| `chat.py` | Gemini client initialization and the `talk()` function |
| `Config.py` | Reads the `google_api_key` environment variable |
| `requirements.txt` | Python dependencies |
| `templates/Home.html` | Home page and prompt submission form |
| `templates/response.html` | Generated response page |
| `README.md` | Project documentation |

Keep the capitalization of `Config.py` and `Home.html` exactly as shown.

## Getting Started

### 1. Download the project

Clone your copy of this repository, or download and extract its ZIP archive. Open a terminal in the project directory containing `app.py` and `requirements.txt`.

You will need Python and pip, internet access, and a Gemini API key with access to the model configured in `chat.py`. Use a Python version compatible with the pinned dependencies; no specific Python version is declared in the supplied project.

### 2. Create and activate a virtual environment

**Windows — PowerShell**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install -r requirements.txt
```

### 4. Configure your API key

Create or manage a key in [Google AI Studio](https://aistudio.google.com/apikey). See Google's [API-key documentation](https://ai.google.dev/gemini-api/docs/api-key) for account setup details.

Set the key in the same terminal session you will use to start the application. Replace `your_api_key_here` with your own key.

**Windows — PowerShell**

```powershell
$env:google_api_key = "your_api_key_here"
```

**Windows — Command Prompt**

```bat
set "google_api_key=your_api_key_here"
```

**macOS / Linux**

```bash
export google_api_key="your_api_key_here"
```

**Use the exact lowercase name `google_api_key`.** The application explicitly reads this name in `Config.py`; it does not rely on the SDK's default environment-variable names. A `.env` file is not loaded by the supplied code.

These commands set the variable for the current terminal session. Never commit your actual API key.

### 5. Check the configured model

`chat.py` currently passes the following model identifier to the SDK:

```python
model='gemini-3.5-flash'
```

This documents the value in the source code, not a guarantee of model availability for your account. If the API reports an unavailable or unsupported model, update this value to a model your account can use with `generate_content`.

### 6. Start the application

```bash
python app.py
```

Open **http://127.0.0.1:5000** in your browser.

The supplied entry point enables Flask debug mode for local development.

## Usage

1. Open the home page.
2. Enter a prompt, such as **“Explain Python lists with a simple example.”**
3. Click **Send to Gemini**.
4. Read the generated response on the response page.
5. Return to the home page using the browser's Back button or by visiting `/` to ask another question.

The response page currently displays the answer without a follow-up input form. Each new submission starts an independent request.

## How It Works

1. Flask serves `Home.html` when the browser requests `/`.
2. The form submits the prompt as a query parameter to `/chat`.
3. `app.py` reads the prompt and calls `talk()` from `chat.py` when it is nonempty.
4. `talk()` calls `client.models.generate_content()` and returns `response.text`.
5. Flask passes the result to `response.html`, where Jinja2 renders it.

The Gemini client is initialized when `chat.py` is imported, so the API key must be configured **before starting the app**.

### Routes

| Method | Route | Behavior |
| --- | --- | --- |
| `GET` | `/` | Renders the home page |
| `GET` | `/chat?prompt=...` | Sends a nonempty prompt to Gemini and renders the response page |

Both routes return HTML. The application does not expose a JSON API. A missing or empty `prompt` skips the Gemini call and renders the response page without an answer.

## Troubleshooting

| Issue | What to check |
| --- | --- |
| `KeyError: 'google_api_key'` | Set the exact environment variable in the terminal running the app, then restart it. |
| `TemplateNotFound` | Place both HTML files inside `templates/` and preserve their filename capitalization. |
| `ModuleNotFoundError` | Activate the virtual environment and install `requirements.txt` using that environment's Python. |
| Dependency installation fails | Check the reported package version, Python compatibility, and package-index access. The dependency pins have not been independently installation-tested for this README. |
| Authentication or permission error | Check the API key and the associated project's access. |
| Model unavailable or unsupported | Check the model identifier in `chat.py` against models accessible to your account. |
| Quota or rate-limit error | Check the API project's quota and usage, then retry when permitted. |
| Blank response page | Check whether a nonempty prompt was submitted and inspect the terminal for API errors. The template hides the response block when the returned text is empty. |

## Current Scope

This is a small learning and portfolio project. The current implementation has:

- No stored chat history, database, or user accounts.
- No streaming responses, Markdown rendering, or syntax highlighting.
- No application-level handling for API failures or server-side prompt-length limits.
- No application-level rate limiting.
- No automated test suite in the supplied files.

Prompts are sent to Google's Gemini service. Because the form uses `GET`, prompt text also appears in the URL and may appear in browser history or server logs. Avoid submitting sensitive information.

For public deployment, disable debug mode, use a production WSGI server, and add appropriate request validation, error handling, and usage controls. Changing prompt submission to `POST` would keep prompts out of query strings. These improvements are not implemented in the current version.

## Screenshots

<!-- Add screenshots to docs/screenshots/ and uncomment the image lines below. -->
<!-- ![Chatbot home page](docs/screenshots/home.png) -->
<!-- ![Generated AI response](docs/screenshots/response.png) -->

## Planned Improvements

- [ ] Add conversation history and multi-turn context.
- [ ] Show friendly error messages for API failures.
- [ ] Submit prompts using POST with server-side validation.
- [ ] Add a follow-up prompt form to the response page.
- [ ] Render Markdown and code blocks safely.
- [ ] Make the model configurable through an environment variable.
- [ ] Add route and chatbot-integration tests using mocked API responses.

## Contributing

Suggestions and improvements are welcome. Open an issue describing the proposed change, or submit a focused pull request with a clear explanation and verification steps. Keep credentials out of commits and examples.

## License and Attribution

No license file was included with the supplied project. Add a `LICENSE` file with your chosen terms before presenting the repository as open source.

The supplied HTML templates include Anudip Foundation attribution in the footer. Review and preserve applicable attribution when publishing the project. Google and Gemini names belong to their respective owners; their use here describes the integration.
