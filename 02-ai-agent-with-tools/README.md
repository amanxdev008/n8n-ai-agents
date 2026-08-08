# AI Agent with Memory and Tools

An advanced n8n AI Agent built with Google Gemini, capable of remembering conversations and taking real actions.

## What it does
- Chats with users and remembers previous messages in the conversation
- Performs calculations using a built-in calculator tool
- Automatically saves user data (name, question, date) to a Google Sheet

## Nodes used
- Chat Trigger
- AI Agent (core)
- Google Gemini Chat Model
- Simple Memory (conversation context)
- Calculator Tool
- Google Sheets Tool (Append Row)

## What I learned
- How to connect an AI Agent to multiple tools
- How to fix common errors (session ID issues, missing API permissions)
- How to use a System Message to guide AI behavior reliably
- Setting up Google Cloud OAuth credentials for Google Sheets integration

This project builds on [01-ai-chatbot](../01-ai-chatbot) and represents my first working "AI Agent" — one that can both talk and act.
