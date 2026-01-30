# AI Chatbot Demo - Quick Setup Guide

## 🚀 Quick Start (5 Minutes)

This guide will help you get the AI Chatbot demo up and running quickly.

## Step 1: Prerequisites Check

Before starting, ensure you have:

- [ ] **Node.js** (v18 or higher) - [Download](https://nodejs.org/)
- [ ] **npm** (v9 or higher) - Comes with Node.js
- [ ] **MongoDB** - [Download](https://www.mongodb.com/try/download/community) or use [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
- [ ] **OpenAI API Key** - [Get one here](https://platform.openai.com/api-keys)

### Verify Installation

```bash
node --version  # Should show v18.x.x or higher
npm --version   # Should show v9.x.x or higher
```

## Step 2: Backend Setup

### 2.1 Install Backend Dependencies

```bash
cd ai-chatbot-demo/backend
npm install
```

### 2.2 Configure Environment

```bash
# Copy example environment file
cp .env.example .env
```

### 2.3 Edit .env File

Open `backend/.env` and add your credentials:

```env
# Required - Get from https://platform.openai.com/api-keys
OPENAI_API_KEY=sk-your-actual-openai-key-here

# Required - MongoDB connection
MONGODB_URI=mongodb://localhost:27017/ai_chatbot

# Optional - For news features (get from https://newsapi.org/)
NEWS_API_KEY=your-newsapi-key-here

# Optional - For Facebook Messenger
FACEBOOK_PAGE_ACCESS_TOKEN=your-token-here
FACEBOOK_VERIFY_TOKEN=your-verify-token-here
```

### 2.4 Create Logs Directory

```bash
mkdir logs
```

### 2.5 Start Backend Server

```bash
# Development mode (recommended for testing)
npm run dev

# OR Production mode
npm start
```

**Expected Output:**
```
🚀 Server running on http://localhost:5000
📊 Environment: development
✅ MongoDB connected successfully
✅ LangChain model initialized successfully
```

## Step 3: Frontend Setup

### 3.1 Open New Terminal

Keep the backend running and open a new terminal window.

### 3.2 Install Frontend Dependencies

```bash
cd ai-chatbot-demo/frontend
npm install
```

### 3.3 Configure Environment

```bash
# Copy example environment file
cp .env.example .env
```

The default configuration should work:
```env
REACT_APP_API_URL=http://localhost:5000/api
```

### 3.4 Start Frontend Server

```bash
npm start
```

**Expected Output:**
```
Compiled successfully!

You can now view ai-chatbot-frontend in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://192.168.x.x:3000
```

## Step 4: Verify Installation

### 4.1 Open Browser

Navigate to: `http://localhost:3000`

### 4.2 Test Features

1. **Home Page**: Should display welcome message and feature cards
2. **Chat Page**: Click "Start Chatting" and send a test message
3. **Survey Page**: View available surveys
4. **News Page**: Browse news articles (requires NewsAPI key)

## Step 5: Optional - WhatsApp Integration

To enable WhatsApp bot:

1. Backend server will display a QR code in the terminal
2. Open WhatsApp on your phone
3. Go to Settings > Linked Devices
4. Scan the QR code
5. WhatsApp bot is now active!

## 🐛 Troubleshooting

### MongoDB Connection Error

**Problem:** `MongoDB connection error`

**Solution:**
```bash
# Check if MongoDB is running
mongod --version

# Start MongoDB service
# On macOS:
brew services start mongodb-community

# On Windows:
net start MongoDB

# On Linux:
sudo systemctl start mongod
```

**Alternative:** Use MongoDB Atlas (cloud)
1. Create free account at https://www.mongodb.com/cloud/atlas
2. Create cluster
3. Get connection string
4. Update `MONGODB_URI` in `.env`

### OpenAI API Error

**Problem:** `Invalid OpenAI API key`

**Solution:**
1. Verify your API key at https://platform.openai.com/api-keys
2. Ensure no extra spaces in `.env` file
3. Restart backend server after updating `.env`

### Port Already in Use

**Problem:** `Port 5000 is already in use`

**Solution:**
```bash
# Change port in backend/.env
PORT=5001

# Update frontend/.env
REACT_APP_API_URL=http://localhost:5001/api
```

### CORS Error

**Problem:** `CORS policy blocked`

**Solution:**
Ensure `FRONTEND_URL` in `backend/.env` matches your frontend URL:
```env
FRONTEND_URL=http://localhost:3000
```

### Dependencies Installation Failed

**Problem:** `npm install` errors

**Solution:**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock.json
rm -rf node_modules package-lock.json

# Reinstall
npm install
```

## 📝 Next Steps

### 1. Create Your First Survey

Use API or create through code:

```javascript
// Example: Create survey via API
POST http://localhost:5000/api/survey/create
{
  "title": "Customer Feedback",
  "description": "We value your opinion",
  "questions": [
    {
      "questionText": "How would you rate our service?",
      "questionType": "rating"
    },
    {
      "questionText": "Would you recommend us?",
      "questionType": "yes_no"
    }
  ]
}
```

### 2. Test AI Chat

1. Navigate to Chat page
2. Send messages like:
   - "Hello, how are you?"
   - "Tell me about AI"
   - "What can you help me with?"

### 3. Explore News Features

1. Get NewsAPI key from https://newsapi.org/
2. Add to `backend/.env`
3. Restart backend
4. Navigate to News page
5. Try different categories and AI summaries

### 4. Customize the Bot

Edit `backend/config/langchain.config.js` to customize AI behavior:

```javascript
temperature: 0.7,  // Lower = more focused, Higher = more creative
maxTokens: 500,    // Maximum response length
```

## 📚 Additional Resources

- **Full Documentation**: See `README.md`
- **API Reference**: Check API endpoints in README
- **LangChain Docs**: https://js.langchain.com/docs/
- **OpenAI API**: https://platform.openai.com/docs/
- **React Docs**: https://react.dev/

## ✅ Success Checklist

- [ ] Backend server running on port 5000
- [ ] Frontend server running on port 3000
- [ ] MongoDB connected successfully
- [ ] OpenAI API key configured
- [ ] Can access homepage at http://localhost:3000
- [ ] Can send chat messages and receive AI responses
- [ ] Can view surveys
- [ ] (Optional) News features working
- [ ] (Optional) WhatsApp bot connected

## 🎉 You're All Set!

Your AI Chatbot demo is now running. Start exploring the features and building amazing conversational AI experiences!

---

**Need Help?** Create an issue in the repository or check the troubleshooting section above.
