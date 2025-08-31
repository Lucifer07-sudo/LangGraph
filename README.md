# COBOL-to-Python Migration Automation

This project provides an automated workflow for migrating legacy COBOL payroll systems into modern Python code, validating correctness via linting, test generation, and debugging.

The workflow is orchestrated using LangGraph with modular tools for each step in the migration pipeline.

### 📌 Features

-- **COBOL File Generator** → Creates large dummy COBOL payroll files (payroll.cbl).

-- **ChromaDB Builder** → Embeds and stores COBOL snippets for retrieval.

-- **Code Search** → Retrieves relevant COBOL snippets from the vector database.

-- **Code Migration** → Converts COBOL into clean, runnable Python code.

-- **Linting** → Ensures generated code has no syntax errors.

-- **Test Generation** → Creates unittest test cases for the migrated Python code.

-- **Test Execution** → Runs tests using pytest.

-- **Debugging** → Iteratively fixes lint/test failures with AI assistance.

-- **Persistence** → Saves final Python code and tests to disk.

📂 Project Structure
```
.
├── main.py                   # Entry point for workflow execution
├── executive_graph_fixed.py  # LangGraph workflow orchestrator
├── generate_payroll_cbl.py   # Script to generate dummy COBOL payroll file
├── build_chroma_db.py        # (⚡ user must run once) build ChromaDB index from COBOL file
│
├── tools/
│   ├── code_search_tool.py   # Vector search tool (Chroma + Azure OpenAI embeddings)
│   ├── code_generator_tool.py # Migrates COBOL to Python
│   ├── linter_tool.py        # Runs pylint to check syntax
│   ├── test_generator_tool.py # Creates unit tests for migrated code
│   ├── test_runner_tool.py   # Executes tests with pytest
│   ├── debug_tool.py         # AI-driven debugging of faulty code
│
├── requirements.txt          # Python dependencies
├── migrated_code.py          # Auto-saved migrated Python program (output)
├── generated_tests.py        # Auto-saved Python unittest suite (output)
```
⚙️ Setup
1. Clone the repo <br>
git clone <your-repo-url> <br>
cd cobol-to-python-migrator <br>

2. Install dependencies <br>
pip install -r requirements.txt

3. Configure environment

Create a .env file with your Azure OpenAI credentials:

OPENAI_API_KEY=your_key <br>
AZURE_ENDPOINT=your_endpoint <br>
OPENAI_API_VERSION= <br>
OPENAI_DEPLOYMENT_NAME_8K=your_model_deployment <br>

🚀 Workflow
Step 1 — Generate COBOL Test File

Create a dummy COBOL payroll system file:

python generate_payroll_cbl.py


This creates payroll.cbl with ~600 lines of COBOL payroll logic.

Step 2 — Build the ChromaDB Index (⚡ Run Once)

Before running migrations, populate ChromaDB with COBOL code embeddings:

python build_chroma_db.py


This will:

Read the COBOL source file (payroll.cbl)

Split into chunks

Embed each chunk with Azure OpenAI embeddings

Store them into a persistent ChromaDB collection (./chroma_db/)

Step 3 — Run Migration Workflow

Run the orchestrator:

python main.py


Flow:

Refine goal into query

Search ChromaDB for COBOL snippets

Migrate COBOL → Python

Lint code

Generate & run tests

Debug until success (max 5 retries)

Save final Python code + tests

🛠️ Example Run
python main.py


Sample Output (simplified):

[Main] Received goal: Migrate the payroll system...
[Executive] Searching code context...
[Executive] Migrating code...
[Executive] Running linter... ✅
[Executive] Generating tests...
[Executive] Running tests... ✅
[Executive] Code and tests saved to disk ✅


Final outputs:

✅ migrated_code.py → Python equivalent of COBOL payroll

✅ generated_tests.py → Automated unit tests

🔄 Iterative Debugging

If linting or tests fail, the system:

Sends errors + faulty code to debug_tool

Updates the code

Re-runs linter/tests until fixed (max 5 retries)

📌 Tech Stack

LangGraph → Workflow orchestration

LangChain + Azure OpenAI → Code migration, debugging, and test generation

ChromaDB → COBOL snippet retrieval via embeddings

pylint / pytest → Static checks and runtime testing
