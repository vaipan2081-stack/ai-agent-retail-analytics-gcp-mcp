## MCP-Enabled AI Agent for Dynamic Data Retrieval and Workflow Automation
<img width="512" height="512" alt="unnamed" src="https://github.com/user-attachments/assets/394d43e5-4f7a-4a2d-8b20-4d327fc8bd79" />
<img width="1200" height="1200" alt="BITS_Pilani-Logo svg" src="https://github.com/user-attachments/assets/fe7fac03-08a4-4e60-8c2f-8d2c82ff79c2" />
![ebo_mart_private_limited_logo](https://github.com/user-attachments/assets/18cea727-690a-4753-bf1a-1674f0efd849)

## 1. Project Overview
This repository contains the complete source code and documentation for an intelligent data analytics system designed to bridge the gap between complex enterprise data warehouses and the non-technical business users who need to derive insights from them.

## The Business Problem
In the modern, fast-paced retail industry, the ability to make rapid, data-driven decisions is paramount. Companies collect vast amounts of operational data in powerful cloud data warehouses like Google BigQuery. This data, covering everything from inventory and store performance to customer behavior, holds the key to competitive advantage. However, a significant challenge persists: this data is often inaccessible to the very stakeholders who need it most, such as CXOs and business analysts. These users typically lack the specialized skills to write complex SQL queries, creating a bottleneck where critical business questions go unanswered or are delayed waiting for technical teams. The core problem is to provide a seamless, secure, and high-performance interface that allows non-developers to trigger sophisticated analytics using intuitive, natural language inputs.   

## The Intelligent Solution
This project implements a robust and intelligent data interaction layer that directly addresses this challenge. It empowers users to ask questions in plain English—for example, "Get reordering configs for B grade outlets"—and receive structured, accurate data in return. This is accomplished through an automated workflow that intelligently translates the user's natural language query into a secure, optimized API call to the underlying data source. The system is designed to be dynamic, low-latency, and highly scalable, ensuring that business leaders have real-time access to key performance indicators without needing to understand the technical complexities of the backend infrastructure.   

## Architectural Philosophy & Significance
This project serves as a practical, end-to-end implementation of the powerful "AI Agent" or "Tool-Augmented LLM" architectural pattern. This paradigm is a significant trend in modern AI development, moving beyond simple text generation to create systems where Large Language Models (LLMs) are given "tools"—in this case, APIs—to interact with and act upon the external world. The architecture is not a simple input-output pipeline but a sophisticated reasoning loop: the LLM receives a user prompt, consults a list of available tools, reasons about which one is appropriate for the task, and then executes it with the correct parameters. This positions the project as a relevant, modern blueprint for building agent-based systems, elevating its value from a specific retail analytics use case to a generalizable framework. The system's modular, decoupled architecture, which separates the data, API, tool abstraction, and AI layers, makes it a highly extensible and reusable foundation for developing other intelligent automation solutions.   

## Project Context
The technology stack reflects a strategic and insightful blend of enterprise-grade cloud services and flexible, open-source tools. The use of Google BigQuery for data warehousing and Google Cloud IAM for security demonstrates a foundation built for enterprise-level scalability, reliability, and governance. Simultaneously, the choice of a self-hosted n8n instance for workflow orchestration and a locally run LLM via Ollama highlights a focus on rapid development, cost control, and developer flexibility. This hybrid approach is a key feature of the project. It originated as an advanced proof-of-concept during a "Practice School I" program by students from BITS Pilani at EBO Mart. This context correctly frames the repository as a powerful and well-architected starter kit, ideal for learning, experimentation, and as a foundation for production systems, rather than a turnkey, out-of-the-box product.   

## 2. Key Features
This system is equipped with a range of features designed to provide a secure, intelligent, and user-friendly data interaction experience.   

NLP-Assisted Querying: At its core, the system translates natural language questions from users into structured SQL queries that are executed against Google BigQuery. This translation is handled by a reasoning AI agent, democratizing data access for non-technical users.   

Dynamic Tool Resolution: The AI agent is not hardcoded to specific tasks. It autonomously selects the correct API endpoint (or "tool") from a list of available functions based on its understanding of the user's intent. This allows the system to be easily extended with new capabilities without altering the core AI logic.   

Standardized Tool Abstraction with MCP: The project utilizes the Model Context Protocol (MCP) and the FastMCP Python SDK to create a formal, discoverable, and interoperable interface for the AI's tools. This crucial layer decouples the AI's reasoning from the specific implementation of the tools, meaning the backend API can be completely refactored without breaking the AI agent, as long as the tool's MCP signature remains consistent.   

Secure Enterprise Data Access: Security is managed at the data source level using Google Cloud Identity and Access Management (IAM). The system authenticates to BigQuery using a scoped-down service account, adhering to the principle of least privilege to ensure data is protected.   

Scalable Microservice API: A lightweight Flask-based API layer acts as the intermediary to the database. This microservice is designed for efficiency and includes performance optimizations such as server-side pagination (LIMIT/OFFSET clauses) to handle large datasets gracefully and minimize payload sizes.   

Containerized & Reproducible Environment: All core workflow services—including n8n, its PostgreSQL database, the MCP Server, and Ollama—are containerized using Docker. This ensures a consistent, isolated, and easily reproducible development environment that can be spun up with a single command, eliminating "it works on my machine" issues.   

Low-Code Workflow Orchestration: The entire prompt-to-response pipeline is orchestrated using n8n, a low-code automation platform. This provides a visual, event-driven interface for building and managing the workflow, making the system's control flow transparent and easy to modify even for those less familiar with the underlying code.   

# 3. System Architecture
The system is designed with a multi-layered, decoupled architecture. Each layer has a distinct responsibility, and together they form a cohesive and robust pipeline that transforms a user's natural language query into a structured data response. This modularity is key to the system's extensibility and maintainability.   

# The Layers
Data Layer (Google BigQuery): This is the foundational layer and the system's single source of truth. It houses the enterprise-scale retail analytics data in structured tables, such as ars_public.reordering_config. All interactions with this layer are performed using parameterized SQL queries, a critical security practice that prevents SQL injection attacks and allows BigQuery's engine to reuse execution plans for better performance.   

API Layer (Flask Microservices): This layer acts as the system's "hands and feet," providing programmatic access to the data layer. It consists of a set of lightweight RESTful microservices built with Flask. These services are responsible for receiving requests, constructing the appropriate SQL query, executing it against BigQuery, and serializing the results into a clean, predictable JSON format for other services to consume. Endpoints are clearly defined for specific data retrieval tasks, such as fetching a record by its ID or searching for records based on filter criteria.   

Tool Abstraction Layer (FastMCP Server): This layer is the system's "universal adapter" and represents a deliberate, sophisticated architectural choice. Instead of having the AI agent directly call the Flask API, which would tightly couple the agent to the API's specific implementation (URLs, authentication, etc.), this layer introduces a formal abstraction using the Model Context Protocol (MCP). The Python functions that call the Flask APIs are wrapped as "tools" using the @mcp.tool() decorator from the FastMCP library. The MCP server then exposes these tools through a standardized JSON-RPC interface. This design choice provides a formal contract. The AI agent only needs to know the tool's abstract signature (its name, description, and parameters) as defined by the MCP server. This decoupling is incredibly powerful: the backend Flask API can be completely rewritten, moved to a different host, or have its authentication method changed, and the AI agent will continue to function perfectly as long as the MCP tool signature remains unchanged. This architectural maturity ensures the system is robust, maintainable, and future-proof.   

Automation & AI Layer (n8n + Ollama): This is the "brain" of the system, where the user's intent is interpreted and the entire workflow is orchestrated. A self-hosted n8n instance provides the visual canvas for the workflow. The process starts with a Chat Trigger node. The user's input is then passed to an AI Agent node, which is powered by a locally running Large Language Model (e.g., Llama 3) served via the Ollama runtime. This agent analyzes the prompt and communicates with the FastMCP server to first discover the available tools and then execute the chosen one. The n8n workflow manages the entire sequence, handling the data flow between the chat interface, the AI agent, and the MCP client, ultimately formatting the final result and returning it to the user.   

4. End-to-End User Workflow
To understand how the architectural layers work in concert, consider the lifecycle of a single user query from start to finish. This "prompt-to-tool execution" pipeline demonstrates the system's dynamic and intelligent nature.   

User Query: A business analyst initiates the process by sending a message to the n8n chat interface. For instance: "Fetch the reordering configuration for ID cfg-a1b2-c3d4".

Workflow Trigger: The Chat Trigger node in the n8n workflow receives this message and activates the execution of the pipeline.

AI Reasoning: The message is passed to the AI Agent node. This node, powered by a local LLM running on Ollama, analyzes the text to decipher the user's intent. It recognizes the keywords "fetch," "reordering configuration," and "ID," and identifies cfg-a1b2-c3d4 as a specific entity.

Tool Discovery: To determine how to fulfill this request, the agent makes a listTools call to the FastMCP Server. The server responds with a structured JSON list of all available tools, including their names and descriptions (e.g., get_reordering_config_by_id: Fetches a single reordering configuration by its unique ID.).

Tool Selection: The LLM processes this list and reasons that the get_reordering_config_by_id tool is the most appropriate match for the user's request. It also correctly extracts 'cfg-a1b2-c3d4' as the value for the id parameter required by that tool.

Tool Execution: The agent now constructs and sends an executeTool request to the FastMCP Server. This is a formal JSON-RPC call containing the tool's name and a dictionary of its arguments: {"tool": "get_reordering_config_by_id", "args": {"id": "cfg-a1b2-c3d4"}}.

API Invocation: The FastMCP Server receives the request and invokes the underlying Python function associated with the get_reordering_config_by_id tool. This function, in turn, makes an HTTP POST request to the corresponding Flask API endpoint (/get_reordering_config_id).

Data Retrieval: The Flask API service receives the request, securely connects to Google BigQuery using its service account credentials, and executes the final, parameterized SQL query to fetch the specific record from the reordering_config table.

Response Cascade: The data begins its journey back. The query result is returned from BigQuery to the Flask API, which formats it as JSON. This JSON response is then passed back to the FastMCP server, which wraps it in an MCP-compliant response and sends it back to the n8n workflow.

Formatted Output: The final n8n node receives the structured JSON data. It can be configured to format this data into a clear, human-readable message, which is then sent back to the user through the chat interface, completing the workflow.

## 5. Technology Stack
The project leverages a carefully selected stack of modern technologies, balancing enterprise-grade cloud services with flexible open-source tools to create a powerful and adaptable system.   

# Role & Purpose
Backend & API	Python 3.x	The primary programming language for all backend services, data processing, and tool logic.
Flask	A lightweight web framework used to build the RESTful API layer that serves data from BigQuery.
Uvicorn	A high-performance ASGI server required to run the asynchronous FastMCP application.
Data & Cloud	Google Cloud Platform (GCP)	The core cloud infrastructure provider for data storage and secure access management.
Google BigQuery	A serverless, enterprise-grade data warehouse serving as the project's primary data source.
Google Cloud IAM	The service used to manage secure, role-based, programmatic access to BigQuery via service accounts.
AI & Automation	n8n (self-hosted)	A low-code platform for visually building, testing, and orchestrating the entire AI workflow from user prompt to final response.
Ollama	A runtime for serving local Large Language Models (e.g., Llama 3) that power the AI agent's reasoning and tool-selection capabilities.
Model Context Protocol (MCP)	The open protocol standard that defines how the AI agent discovers and communicates with its external tools in a formal, decoupled manner.
FastMCP	The specific Python SDK used to implement the MCP server, wrapping the project's Python functions as discoverable and callable "tools".
DevOps & Testing	Docker & Docker Compose	Containerizes all workflow services for an isolated, portable, and reproducible one-command setup.
Postman	Used for manually testing and validating the Flask API endpoints and the JSON-RPC calls to the MCP tool server.
Python unittest	The standard library framework used for writing unit tests for core Python functions and business logic.

Export to Sheets
6. Prerequisites
Before you begin the installation process, ensure you have the following accounts, software, and configurations in place.   

Accounts:

A Google Cloud Platform (GCP) Account with an active billing profile.

Software:

Docker and Docker Compose installed and running on your local machine.

Python 3.10 or newer, along with pip for package management.

Git for cloning the repository.

Ollama runtime installed and running. Instructions can be found at ollama.com.

Cloud Configuration:

A GCP Project must be created or selected.

The BigQuery API must be enabled for your GCP project.

A GCP Service Account must be created. It requires the following IAM roles:

BigQuery Data Viewer

BigQuery Job User

A JSON key file for this service account must be generated and downloaded to your local machine.

Local LLM:

A Large Language Model must be pulled and available via Ollama. You can do this by running the following command in your terminal:

Bash

ollama pull llama3
7. Installation and Configuration
Follow these steps to get the entire system up and running on your local machine. This guide assumes all prerequisites have been met.   

Clone the Repository
Open your terminal and clone this repository to your local machine.

Bash

git clone https://github.com/your-username/ai-agent-retail-analytics-gcp.git
cd ai-agent-retail-analytics-gcp
Configure Environment Variables

Move the GCP service account JSON key file you downloaded into the root of the project directory. For this guide, we'll assume it is named gcp-credentials.json.

Important: Ensure this filename is added to your .gitignore file to prevent accidentally committing credentials to version control.

Create a .env file in the root directory by copying the example file:

Bash

cp.env.example.env
Open the .env file and set the GOOGLE_APPLICATION_CREDENTIALS variable to the name of your JSON key file:

GOOGLE_APPLICATION_CREDENTIALS=gcp-credentials.json
GCP_PROJECT_ID=your-gcp-project-id
Replace your-gcp-project-id with your actual GCP Project ID.

Launch Containerized Services
This project uses Docker Compose to manage the n8n workflow engine and its dependencies. Launch them in detached mode:

Bash

docker-compose up -d
This command will start the n8n container along with a PostgreSQL database for its data.

Set Up the Python Backend
The backend consists of the Flask API server and the FastMCP tool server. Set up a dedicated Python virtual environment for them.

Bash

# Create a virtual environment
python3 -m venv venv

# Activate the virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
#.\venv\Scripts\activate

# Install the required Python packages
pip install -r requirements.txt
Run the Backend Servers
You will need two separate terminal windows for this step, both with the virtual environment activated.

In Terminal 1 (Run the Flask API Server):

Bash

python flask_api.py
You should see output indicating the Flask server is running, typically on port 5000.

In Terminal 2 (Run the FastMCP Tool Server):

Bash

python mcp_server.py
You should see output from Uvicorn indicating the MCP server is running, typically on port 8000.

Configure the n8n Workflow

Access the n8n web UI by navigating to http://localhost:5678 in your web browser.

Create a new workflow.

Import the workflow definition provided in this repository (n8n_workflow.json).

Crucial Step: Open the MCP Client node within the imported workflow. You need to configure its URL to point to the MCP server running on your host machine. Because n8n is running inside a Docker container, you cannot use localhost. Instead, use the special Docker host address:

URL: http://host.docker.internal:8000/stream

Activate the workflow using the toggle switch in the n8n UI.

Your system is now fully configured and ready for use.

8. Usage Guide
With all services running, you can interact with the AI agent through the n8n chat interface. Navigate to your n8n workflow and use the chat panel to send queries. Here are some examples to try :   

To fetch a specific configuration by its ID:

"Get me the reordering config with id abc-123-def"

To perform a filtered search:

"Show all reordering configs for site type 'A' and sku grading 'premium'"

To ask the agent about its capabilities:

"What tools do you have?"

The agent will process your request, execute the appropriate tool, and return the formatted data directly in the chat window.

9. API Endpoint Documentation
While the primary interaction with the system is through natural language, developers may need to interact with or extend the underlying Flask API directly. The following table documents the contract for the core API endpoints that serve as the agent's "tools".   

Method	Endpoint	Body Parameters	Description	Example Success Response
POST	/get_reordering_config_id	{"id": "string"}	Fetches a single reordering configuration by its unique ID.	{"status": "success", "count": 1, "result": [{"reordering_config_id": "...",...}]}
POST	/get_reorder_configs	{"site_type": "string", "sku_grading": "string", "page": int, "page_size": int}	Fetches a paginated list of configurations matching the specified filters. page and page_size are optional and default to 1 and 10, respectively.	{"status": "success", "count": 10, "total": 50, "result": [{...}, {...}]}


12. Acknowledgements
This repository is a developer-focused adaptation and extension of a project originally completed by Vaibhav Panda and Setu Minocha for the BITS Pilani Practice School I program at EBO Mart. The original academic report serves as the foundational source for this documentation and architecture.   


