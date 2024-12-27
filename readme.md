# Local LLM Setup Guide 

Local LLM Environment Setup Guide
=================================

Introduction
------------

In line with experimenting with LLMs locally, this guide details setting up a local lab environment. Using a new M4 Mini with 64 GB of unified memory enables running substantial models on the local network. To address remote access needs, the setup incorporates Tailscale for private networking via Wireguard, creating a private mesh that allows secure LLM access from anywhere without public internet exposure.

System Overview
---------------

This guide describes how to set up a local LLM environment using a Mac Mini as the server, with both local and remote access capabilities.

### Physical Architecture

![Physical Architecture Diagram showing Mac Mini M4, router, and devices](llm-setup.png)

Physical setup showing Mac Mini M4 (64GB) connected to local network and devices

### Key Components:

*   **Ollama:** Running on Mac Mini to serve LLM models
*   **Tailscale:** For secure network connectivity
*   **LLM CLI:** Command-line interface for model interaction
*   **Enchanted:** iOS client for mobile access
*   **UV:**Python package and project manager, written in Rust


Prerequisites
-------------

### Hardware

*   Mac Mini M4 with 64GB unified memory
*   iOS device for mobile access
*   Laptop
*   Local router with Ethernet/WiFi

### Network

*   Stable internet connection
*   Administrator access on all devices


Step-by-Step Setup Instructions
-------------------------------

### 1\. Install Ollama on Mac Mini

Download and install Ollama from the official website:

    curl -fsSL https://ollama.com/install.sh | sh

Verify installation:

    ollama --version

### 2\. Pull LLM Models

Download your preferred models. For example:

    # Pull recommended models
    ollama pull llama3.2-vision:latest
    ollama pull smollm2:latest
    ollama pull Qwen2.5-Coder:latest
    ollama pull llama3.3:latest

### 3\. Setup Tailscale

1.  Download Tailscale from [tailscale.com](https://tailscale.com/download)
2.  Install on both Mac Mini and iOS device
3.  Sign in with your Tailscale account on both devices
4.  Note the Tailscale IP of your Mac Mini

### 4\. Install [UV Package Manager](https://astral.sh/blog/uv)

Install UV using the official installation script:

    curl -LsSf https://astral.sh/uv/install.sh | sh

Verify installation:

    uv --version

### 5\. Install LLM CLI Tool with UV

Install Simon Willison's LLM tool on my laptop using UV and use the [llm-ollama](https://github.com/taketwo/llm-ollama) to connect to ollama:

    # Create and activate new virtual environment
    uv venv .venv
    source .venv/bin/activate
    
    # Install LLM and Ollama plugin
    uv pip install llm llm-ollama

Configure LLM with UV for Ollama access:

    # Set up alias for easy access
    alias ll33='OLLAMA_HOST=http://[tailscale-ip]:11434 uvx --with llm --with llm-ollama llm -m llama3.3'
    
    # Test the connection
    ll33 "Hello, world!"

### 6\. Setup Enchanted iOS App

1.  Download Enchanted from the App Store
2.  Open the app and go to Settings
3.  Add new server with your Tailscale IP
4.  Configure the endpoint: http://\[tailscale-ip\]:11434/api

### ⚠️ Important Security Notes

*   Ensure your Tailscale network is properly configured for security
*   Keep your Ollama installation updated
*   Regularly update your LLM models
*   Monitor system resources on your Mac Mini

Summary & Lessons Learned.
--------------------------

With this setup I can reach my private llm setup from anywhere on any device I choose to secure. Tailscale makes it easy to connect peer-to-peer wireguard connections for private networking allowing me to access self-hosted models without making them publically available or opening up my network attack surface. I can now grab and test new models as quickly as I can find them! The Mac Mini is tiny, quiet and sips power, at some point I may want to upgrade to a serious GPU system but for now it meets my needs very well. I'm starting to get the hang of uv and using it to quickly test new packages for python has also been fun.

Troubleshooting
---------------

🔍 Network Connectivity Issues

*   Verify Tailscale is running on both devices
*   Check Ollama service is running: `sudo lsof -i :11434`
*   Test connectivity: `ping [tailscale-ip]`
*   Verify no firewall blocking: `sudo lsof -i :11434`
*   By default MacOS will put the Mac Mini to sleep when not in use, make sure to change your energy settings to make sure the system is always available.

🔍 Model Loading Issues

*   Check model status: `ollama list`
*   Verify disk space: `df -h`
*   Check model pulling: `ollama pull [model] --verbose`

