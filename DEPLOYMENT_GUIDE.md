# 🚀 AI Pipe API Migration & Hugging Face Deployment Guide

## 📋 Summary of Changes

This document summarizes all changes made to migrate from Google Gemini API to AI Pipe API and provides detailed deployment instructions for Hugging Face Spaces.

## ✅ Changes Made

### 1. **API Migration (Gemini → AI Pipe)**

#### Files Modified:
- `agent.py` - Updated LLM initialization to use AI Pipe API
- `llm-analysis-quiz-solver/agent.py` - Same updates for subdirectory
- `tools/run_code.py` - Removed unused Google imports
- `llm-analysis-quiz-solver/tools/run_code.py` - Same cleanup
- `requirements.txt` - Replaced Google dependencies with OpenAI SDK
- `llm-analysis-quiz-solver/requirements.txt` - Same updates
- `pyproject.toml` - Updated dependencies
- `llm-analysis-quiz-solver/pyproject.toml` - Same updates
- `README.md` - Updated documentation

#### Key Changes:
1. **LLM Provider**: Changed from `google_genai` to `openai` provider
2. **Model**: Changed from `gemini-2.5-flash` to `gpt-4o-mini` (AI Pipe compatible)
3. **API Configuration**: Added `AIPIPE_API_KEY` and `AIPIPE_BASE_URL` environment variables
4. **Dependencies**: 
   - Removed: `langchain-google-genai`, `google-genai`
   - Added: `langchain-openai`, `openai`

### 2. **Environment Variables**

**Old Configuration:**
```env
GOOGLE_API_KEY=your_gemini_api_key_here
```

**New Configuration:**
```env
AIPIPE_API_KEY=your_aipipe_api_key_here
AIPIPE_BASE_URL=https://aipipe.org/openrouter/v1  # Optional, defaults to this value (OpenRouter-compatible endpoint)
```

## 🔧 Setup Instructions

### Local Development

1. **Install Dependencies:**
   ```bash
   pip install -r requirements.txt
   # or
   uv sync
   ```

2. **Create `.env` file:**
   ```env
   EMAIL=your_iitm_email@ds.study.iitm.ac.in
   SECRET=your_secret_key
   AIPIPE_API_KEY=your_aipipe_api_key_here
   AIPIPE_BASE_URL=https://aipipe.org/openrouter/v1
   ```

3. **Get AI Pipe API Key:**
   - Visit [AI Pipe](https://aipipe.org)
   - Sign up for an account or log in
   - For backend Python usage, you'll need to get an API token/key
   - The API uses OpenRouter-compatible endpoints at `https://aipipe.org/openrouter/v1`
   - **Note**: The JavaScript examples show client-side browser auth with `getProfile()`, but for Python backend, you need to use a direct API key/token as `AIPIPE_API_KEY` environment variable
   - Check AI Pipe documentation for how to generate backend API keys

4. **Run the server:**
   ```bash
   uv run main.py
   ```

## 🤗 Hugging Face Spaces Deployment - Detailed Steps

### Prerequisites
- Hugging Face account ([sign up here](https://huggingface.co/join))
- Git installed on your machine
- Your project code ready

### Step-by-Step Deployment

#### Step 1: Create a Hugging Face Space

1. Go to [Hugging Face](https://huggingface.co/) and sign in
2. Click on your profile picture (top right)
3. Select **"New Space"**
4. Fill in the form:
   - **Space name**: `llm-quiz-solver` (or your preferred name)
   - **SDK**: Select **"Docker"** (important!)
   - **Visibility**: 
     - **Public**: Anyone can see your code and use the app
     - **Private**: Only you can access it
   - **Hardware**: 
     - **CPU basic**: Free tier (may have limitations)
     - **CPU upgrade**: Better performance (paid)
     - **GPU**: For heavy ML workloads (paid)
5. Click **"Create Space"**

#### Step 2: Prepare Your Repository

Your repository should have:
- `Dockerfile` in the root directory
- `requirements.txt` or `pyproject.toml`
- All source code files
- `.gitignore` (to exclude `.env` files)

#### Step 3: Push Code to Hugging Face

**Option A: Direct Git Push (Recommended)**

1. In your local repository, add Hugging Face as a remote:
   ```bash
   git remote add hf https://huggingface.co/spaces/YOUR_USERNAME/llm-quiz-solver
   ```
   Replace `YOUR_USERNAME` with your Hugging Face username.

2. Push your code:
   ```bash
   git push hf main
   ```
   If your default branch is `master`, use:
   ```bash
   git push hf master:main
   ```

**Option B: Upload via Web Interface**

1. In your Space, go to **"Files and versions"** tab
2. Click **"Add file"** → **"Upload files"**
3. Drag and drop all your project files
4. Click **"Commit changes"**

> ⚠️ **Note**: For large files or frequent updates, Git push is recommended.

#### Step 4: Verify Dockerfile

Ensure your `Dockerfile` is correct. It should:
- Use Python 3.10+
- Install system dependencies (Playwright, Tesseract)
- Install project dependencies
- Expose port 7860 (Hugging Face default)
- Run your FastAPI app

Example Dockerfile (already in your project):
```dockerfile
FROM python:3.10-slim

RUN apt-get update && apt-get install -y \
    wget gnupg ca-certificates curl unzip \
    libnss3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libxkbcommon0 \
    libgtk-3-0 libgbm1 libasound2 libxcomposite1 libxdamage1 libxrandr2 \
    libxfixes3 libpango-1.0-0 libcairo2 \
    tesseract-ocr \
    && rm -rf /var/lib/apt/lists/*

RUN pip install playwright && playwright install --with-deps chromium
RUN pip install uv

WORKDIR /app
COPY . .

ENV PYTHONUNBUFFERED=1
ENV PYTHONIOENCODING=utf-8

RUN uv sync --frozen

EXPOSE 7860

CMD ["uv", "run", "main.py"]
```

#### Step 5: Configure Environment Variables (Secrets)

1. In your Space, go to **"Settings"** tab
2. Scroll down to **"Repository secrets"** section
3. Click **"New secret"** for each variable:

   **Secret 1: EMAIL**
   - Name: `EMAIL`
   - Value: `your_iitm_email@ds.study.iitm.ac.in`
   - Click **"Save"**

   **Secret 2: SECRET**
   - Name: `SECRET`
   - Value: `your_secret_key`
   - Click **"Save"**

   **Secret 3: AIPIPE_API_KEY**
   - Name: `AIPIPE_API_KEY`
   - Value: `your_aipipe_api_key_from_aipipe.io`
   - Click **"Save"**

   **Secret 4: AIPIPE_BASE_URL** (Optional)
   - Name: `AIPIPE_BASE_URL`
   - Value: `https://aipipe.org/openrouter/v1`
   - Click **"Save"**

> 🔐 **Security Note**: 
> - Secrets are encrypted and only accessible at runtime
> - Never commit API keys or secrets to your repository
> - Use `.gitignore` to exclude `.env` files

#### Step 6: Configure Space Settings

1. In **"Settings"** tab, configure:

   **Hardware:**
   - **CPU basic**: Free tier, suitable for testing
   - **CPU upgrade**: Better performance, recommended for production
   - **GPU**: Only needed for heavy ML workloads

   **Sleep time:**
   - **After 5 minutes**: App sleeps after inactivity (saves resources)
   - **Never**: App always runs (uses more resources, faster response)

   **Storage:**
   - Default is usually sufficient (50GB on free tier)

2. Click **"Save"** after making changes

#### Step 7: Build and Deploy

1. Go to **"App"** tab in your Space
2. Hugging Face will automatically:
   - Detect your Dockerfile
   - Build the Docker image
   - Install all dependencies
   - Start your FastAPI server

3. Monitor the build process:
   - Go to **"Logs"** tab to see build logs
   - Wait for "Build successful" message
   - This may take 5-15 minutes on first build

4. Once built, your app will be available at:
   ```
   https://YOUR_USERNAME-llm-quiz-solver.hf.space
   ```

#### Step 8: Verify Deployment

**Test Health Endpoint:**
```bash
curl https://YOUR_USERNAME-llm-quiz-solver.hf.space/healthz
```

Expected response:
```json
{"status": "ok", "uptime_seconds": 123}
```

**Test Solve Endpoint:**
```bash
curl -X POST https://YOUR_USERNAME-llm-quiz-solver.hf.space/solve \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your_email@ds.study.iitm.ac.in",
    "secret": "your_secret",
    "url": "https://tds-llm-analysis.s-anand.net/demo"
  }'
```

Expected response:
```json
{"status": "ok"}
```

#### Step 9: Monitor Your Deployment

**Logs Tab:**
- View real-time application logs
- Debug errors and issues
- Monitor request/response cycles

**Metrics Tab:**
- CPU usage
- Memory usage
- Network traffic
- Request count

**Settings Tab:**
- Restart your app if needed
- View build history
- Manage secrets

## 🔍 Troubleshooting

### Issue: Build Fails

**Symptoms:**
- Build logs show errors
- App doesn't start

**Solutions:**
1. Check Dockerfile syntax
2. Verify all dependencies are in `requirements.txt` or `pyproject.toml`
3. Check build logs for specific error messages
4. Ensure Python version is compatible (3.10+)

### Issue: "Out of Memory" Error

**Symptoms:**
- Build fails with memory errors
- App crashes during runtime

**Solutions:**
1. Upgrade to **CPU upgrade** or **GPU** tier
2. Optimize Dockerfile to reduce image size
3. Remove unused dependencies

### Issue: Playwright Browser Not Found

**Symptoms:**
- Errors about Chromium not found
- Web scraping fails

**Solutions:**
1. Ensure `playwright install --with-deps chromium` is in Dockerfile
2. Verify system dependencies are installed
3. Check Playwright version compatibility

### Issue: Environment Variables Not Working

**Symptoms:**
- API calls fail
- "API key not found" errors

**Solutions:**
1. Verify secrets are set in **Settings → Repository secrets**
2. Check secret names match exactly (case-sensitive)
3. Restart the app after adding secrets
4. Check logs for environment variable errors

### Issue: Port Conflicts

**Symptoms:**
- App doesn't start
- Connection refused errors

**Solutions:**
1. Ensure Dockerfile exposes port 7860
2. Verify FastAPI/uvicorn uses port 7860
3. Check if another service is using the port

### Issue: App Sleeps After Inactivity

**Symptoms:**
- First request after inactivity is slow
- App appears unresponsive

**Solutions:**
1. Set sleep time to **"Never"** in Settings
2. Note: This uses more resources but provides instant responses
3. Consider using a health check ping to keep it awake

### Issue: Slow Response Times

**Symptoms:**
- Requests take too long
- Timeout errors

**Solutions:**
1. Upgrade hardware tier
2. Optimize code and dependencies
3. Check AI Pipe API response times
4. Monitor resource usage in Metrics tab

## 🔄 Updating Your Deployment

To update your deployed app:

1. **Make changes locally:**
   ```bash
   git add .
   git commit -m "Your update message"
   ```

2. **Push to Hugging Face:**
   ```bash
   git push hf main
   ```

3. **Automatic rebuild:**
   - Hugging Face detects the push
   - Automatically rebuilds the Docker image
   - Redeploys the app
   - Usually takes 5-10 minutes

4. **Monitor the process:**
   - Check **"Logs"** tab for build progress
   - Wait for "Build successful" message

## 📝 Additional Tips

### Custom Domain
- In Settings, you can add a custom domain
- Requires domain verification
- Useful for production deployments

### API Documentation
- FastAPI automatically generates docs
- Available at: `https://YOUR_SPACE.hf.space/docs`
- Interactive API testing interface

### Resource Management
- Free tier has limits on CPU/memory
- Monitor usage in Metrics tab
- Upgrade if you hit limits frequently

### Backup Strategy
- Keep local copies of your code
- Use Git for version control
- Export secrets securely (password manager)

## 🎉 Success Checklist

- [ ] Space created on Hugging Face
- [ ] Code pushed to Space
- [ ] Dockerfile verified
- [ ] Environment variables (secrets) configured
- [ ] Build successful
- [ ] Health endpoint working (`/healthz`)
- [ ] Solve endpoint tested (`/solve`)
- [ ] Logs show no errors
- [ ] Metrics show normal resource usage

## 📞 Support

If you encounter issues:

1. **Check Logs**: First place to look for errors
2. **Review Documentation**: [Hugging Face Spaces Docs](https://huggingface.co/docs/hub/spaces)
3. **Community**: [Hugging Face Forums](https://discuss.huggingface.co/)
4. **AI Pipe Support**: Contact AI Pipe support for API issues

## 🔗 Useful Links

- [Hugging Face Spaces](https://huggingface.co/spaces)
- [AI Pipe API Documentation](https://aipipe.org/docs)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [LangChain Documentation](https://python.langchain.com/)

---

**Last Updated**: 2025-01-XX
**Migration Status**: ✅ Complete
**Deployment Ready**: ✅ Yes

