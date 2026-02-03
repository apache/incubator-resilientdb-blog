---
layout: article
title: "ResShare: A Decentralized File Sharing Network Built on ResilientDB and IPFS"
author: Jiazhi Sun, Devang Borkar
tags: ResilientDB IPFS Decentralized File Sharing ResShare
aside:
   toc: true
article_header:
   type: overlay
   theme: dark
   background_color: '#000'
   background_image:
      gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
      src: assets/images/resdb-gettingstarted/code_close_up.jpeg
---

# ResShare - Decentralized File Sharing Application with AI Chatbot

ResShare is a decentralized file sharing application that allows users to securely store, share, and manage their files using Resilient DB technology. The application now features an **AI-powered chatbot** that can answer questions about your uploaded documents using Retrieval-Augmented Generation (RAG).

## Features

### Core Features
- User Authentication (Sign up, Login, Logout)
- File Upload and Download
- Folder Creation and Management
- File Sharing between Users
- Secure File Storage using IPFS
- User Account Management

### AI Document Assistant
- **Intelligent Document Q&A**: Ask questions about your uploaded files
- **Multi-format Support**: Works with PDF, DOCX, and TXT files
- **Semantic Search**: Finds relevant information across all your documents
- **Privacy-First**: Your data stays isolated and secure
- **Source Attribution**: See which documents were used to answer your questions

## Tech Stack

- **Frontend**: React.js with Material-UI
- **Backend**: Python Flask
- **Storage**: ResilientDB for Metadata storage and IPFS for File Storage
- **Authentication**: Session-based authentication
- **AI/ML**: 
  - Sentence Transformers for embeddings
  - FAISS for vector search
  - Gemini GPT (optional) or local models for response generation
  - LangChain for text processing

## Prerequisites

- Python 3.8+
- Node.js 16+ and npm
- IPFS daemon running locally
- (Optional) Gemini API key for enhanced AI responses

## Installation

1. **Clone the repository:**
```bash
git clone https://github.com/NoBugInMyCode/ResShareDeployable.git
cd ResShareDeployable
```

2. **Install backend dependencies:**
```bash
pip install -r requirements.txt
```

3. **Install frontend dependencies:**
```bash
cd frontend
npm install
cd ..
```

4. **Set up AI features (Optional but recommended):**
```bash
# For enhanced AI responses, set your Gemini API key
export GOOGLE_API_KEY="your-gemini-api-key-here"
```

## Running the Application

1. **Start the IPFS daemon:**
```bash
ipfs daemon
```
*To install IPFS Cluster Service, please refer to [this link](https://ipfscluster.io/download/)*

2. **Start the backend server:**
```bash
python app.py
```

3. **Start the frontend development server:**
```bash
cd frontend
npm start
```

The application will be available at:
- Frontend: http://localhost:3000
- Backend API: http://localhost:5000

## Using the AI Chatbot

### Getting Started with AI
1. **Upload Documents**: Upload PDF, DOCX, or TXT files through the normal file upload process
2. **Navigate to AI Chat**: Click the "AI Chat" button in the navigation bar
3. **Ask Questions**: Type questions about your documents and get intelligent responses

### Example Queries
- "What are the main findings in my research paper?"
- "Summarize the key points from my meeting notes"
- "What does my contract say about payment terms?"
- "Find information about project deadlines"

### Supported File Types for AI
- **PDF**: Research papers, reports, contracts
- **DOCX**: Word documents, meeting notes, proposals  
- **TXT**: Plain text files, code documentation, notes

### AI Features
- **Smart Chunking**: Documents are intelligently split into semantic chunks
- **Vector Search**: Uses advanced embeddings to find relevant content
- **Source Attribution**: Shows which files and sections were used for answers
- **Privacy Preserved**: Each user's AI data is completely isolated

## Configuration

### Basic Configuration (No API key required)
The AI chatbot works out of the box with:
- Local sentence transformer models for embeddings
- FAISS for fast vector search
- Simple extractive responses

### Enhanced Configuration (With Gemini)
For higher quality responses, set up Gemini:
```bash
export GOOGLE_API_KEY="sk-your-key-here"
```

## Usage

### Standard File Operations
1. Create a new account using the sign-up feature
2. Log in to your account
3. Create folders to organize your files
4. Upload files to your folders
5. Share files with other users
6. Download shared files from other users

### AI-Powered Features
1. **Upload Text Documents**: Upload PDF, DOCX, or TXT files
2. **Wait for Processing**: Files are automatically processed for AI search
3. **Ask Questions**: Use the AI Chat interface to query your documents
4. **Get Intelligent Answers**: Receive responses with source attribution
5. **Explore Knowledge Base**: View statistics about your indexed documents
## Architecture

### RAG Pipeline
```
File Upload → Text Extraction → Chunking → Embedding → Vector DB Storage
                                                            ↓
User Query → Query Embedding → Vector Search → Context → LLM → Response
```

### Components
- **Text Extractors**: PDF (PyPDF2), DOCX (python-docx), TXT (UTF-8)
- **Chunking**: LangChain RecursiveCharacterTextSplitter
- **Embeddings**: Google Gemini API (gemini-embedding-001) with configurable dimensions (768/1536/3072)
- **Vector DB**: FAISS with per-user isolation
- **LLM**: Gemini 2.5 Flash (optional) or extractive fallback

