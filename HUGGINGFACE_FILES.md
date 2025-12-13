# 📦 Files to Upload to Hugging Face Spaces

## ✅ Required Files (Must Upload)

### Root Directory Files:
```
Dockerfile                    # Docker configuration (REQUIRED)
main.py                       # FastAPI entry point (REQUIRED)
agent.py                      # LangGraph agent logic (REQUIRED)
shared_store.py               # Shared state management (REQUIRED)
pyproject.toml                # Dependencies configuration (REQUIRED)
requirements.txt              # Alternative dependencies file (REQUIRED if not using pyproject.toml)
__init__.py                   # Python package marker
```

### Tools Directory (Complete):
```
tools/
├── __init__.py              # Package marker
├── add_dependencies.py      # Dependency installer tool
├── audio_transcribing.py    # Audio transcription tool
├── download_file.py         # File downloader tool
├── encode_image_to_base64.py # Image encoder tool
├── image_content_extracter.py # OCR tool
├── run_code.py              # Code execution tool
├── send_request.py          # HTTP request tool
└── web_scraper.py           # Web scraping tool
```

## 📋 Complete File List for Upload

```
📁 Root Directory:
├── Dockerfile
├── main.py
├── agent.py
├── shared_store.py
├── pyproject.toml
├── requirements.txt
├── __init__.py
└── README.md (REQUIRED - must have YAML front matter for HF Spaces)

📁 tools/
├── __init__.py
├── add_dependencies.py
├── audio_transcribing.py
├── download_file.py
├── encode_image_to_base64.py
├── image_content_extracter.py
├── run_code.py
├── send_request.py
└── web_scraper.py
```

## ❌ Files NOT to Upload

```
.env                          # Secrets go in HF Settings → Secrets
test_client.py                # Testing file
test_requests.bat            # Testing file
postman_requests.json        # Testing file
DEPLOYMENT_GUIDE.md          # Documentation (not needed for deployment)
llm-analysis-quiz-solver/     # Duplicate subdirectory (not needed)
uv.lock                      # Can be regenerated
__pycache__/                 # Auto-generated (excluded by .gitignore)
*.pyc                        # Compiled Python files
.venv/                       # Virtual environment
LLMFiles/                    # Runtime generated files
```

## 🚀 Quick Upload Checklist

- [ ] `Dockerfile`
- [ ] `main.py`
- [ ] `agent.py`
- [ ] `shared_store.py`
- [ ] `pyproject.toml`
- [ ] `requirements.txt`
- [ ] `__init__.py` (root)
- [ ] `tools/` directory (all 9 files)
- [ ] `README.md` (REQUIRED - must include YAML front matter)

## 📝 Notes

1. **Dockerfile is CRITICAL** - Without it, Hugging Face won't know how to build your app
2. **README.md MUST have YAML front matter** - Hugging Face Spaces requires this configuration at the top:
   ```yaml
   ---
   title: LLM Quiz Solver
   emoji: 🤖
   colorFrom: blue
   colorTo: purple
   sdk: docker
   sdk_version: latest
   app_file: main.py
   pinned: false
   ---
   ```
3. **All tools must be included** - The agent depends on all tools in the `tools/` directory
4. **Dependencies files** - Include both `pyproject.toml` and `requirements.txt` for compatibility
5. **No .env file** - Set environment variables in Hugging Face Settings → Repository secrets
6. **No test files** - They're not needed for deployment

## 🔧 Environment Variables to Set in HF Settings

After uploading files, add these secrets in **Settings → Repository secrets**:

- `EMAIL`
- `SECRET`
- `AIPIPE_API_KEY`
- `AIPIPE_BASE_URL` (optional, defaults to `https://aipipe.org/openrouter/v1`)

