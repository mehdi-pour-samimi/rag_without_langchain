📚 Document-Based Question Answering System (RAG)

A simple Retrieval-Augmented Generation (RAG) application that allows users to extract information from PDF, DOCX, and TXT documents, store document embeddings in ChromaDB, and answer questions using semantic search and a Large Language Model (LLM).

This project combines Sentence Transformers, ChromaDB, and OpenRouter to build a document-based question-answering system that generates answers grounded in the provided documents.

🚀 Features
Multiple Document Formats: Supports PDF, DOCX, and TXT files.
Text Extraction: Automatically extracts text from uploaded documents.
Text Chunking: Splits large documents into smaller, overlapping chunks.
Semantic Embeddings: Uses the all-MiniLM-L6-v2 Sentence Transformer model to generate embeddings.
Vector Database: Stores document chunks and their embeddings in ChromaDB.
Semantic Search: Retrieves the most relevant document chunks based on user queries.
Retrieval-Augmented Generation: Uses OpenRouter-hosted LLMs to generate context-aware answers.
Source Tracking: Displays the original document name and chunk number associated with retrieved information.
Persistent Storage: Stores vector database data locally using ChromaDB's persistent client.
🏗️ Architecture

The project follows a Retrieval-Augmented Generation (RAG) pipeline:

text
              Input Documents
              (PDF / DOCX / TXT)
                      |
                      v
               Text Extraction
                      |
                      v
                Text Chunking
                      |
                      v
             Sentence Transformer
              (all-MiniLM-L6-v2)
                      |
                      v
                ChromaDB
               Vector Storage
                      |
                      |
User Query ----------+
      |
      v
Semantic Search
      |
      v
Retrieve Relevant Chunks
      |
      v
Build Context + Sources
      |
      v
    OpenRouter
      |
      v
  Large Language Model
      |
      v
  Context-Based Answer
      |
      v
 Display Answer + Sources

🛠️ Technologies Used
Technology	Purpose
Python	Main programming language
ChromaDB	Vector database and similarity search
Sentence Transformers	Text embedding generation
all-MiniLM-L6-v2	Embedding model
OpenRouter	LLM API access
OpenAI Python SDK	Communicating with OpenRouter
PyPDF2	PDF text extraction
python-docx	Word document text extraction
gdown	Downloading documents from Google Drive
📦 Installation
1. Clone the Repository
bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY


Replace USERNAME and REPOSITORY with your GitHub username and repository name.

2. Install Dependencies
bash
pip install -U chromadb
pip install -U openai
pip install -U pypdf2
pip install -U python-docx
pip install -U sentence-transformers
pip install -U gdown

🔑 API Configuration

This project uses OpenRouter to access a Large Language Model.

Create an account at OpenRouter.
Generate an API key from your OpenRouter account.
Configure the API key using an environment variable.
python
import os
from openai import OpenAI

OPEN_ROUTER_API_KEY = os.getenv("OPENROUTER_API_KEY")

OPEN_ROUTER_MODEL_NAME = "openai/gpt-oss-120b:free"

client = OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=OPEN_ROUTER_API_KEY
)


Set your environment variable before running the application:

bash
export OPENROUTER_API_KEY="your_api_key"


Note: Never upload your API key to GitHub. Use environment variables or a .env file excluded through .gitignore.

📄 Supported Document Formats

The application supports the following file extensions:

.pdf – PDF documents
.docx – Microsoft Word documents
.txt – Plain text files

The read_document() function automatically identifies the file format and calls the appropriate extraction function.

⚙️ How It Works
1. Document Loading and Text Extraction

The application extracts text from different document formats using dedicated functions:

read_text_file() – Reads TXT files.
read_pdf_file() – Extracts text from PDF files using PyPDF2.
read_docx_file() – Extracts paragraphs from DOCX files using python-docx.
read_document() – Automatically selects the appropriate extraction method.
2. Text Chunking

Long documents are divided into smaller chunks to improve retrieval performance.

Default configuration:

python
chunk_size = 500
chunk_overlap = 100


The overlapping chunks help preserve contextual information between consecutive text segments.

3. Embedding Generation and Vector Storage

The project uses the Sentence Transformer model:

python
all-MiniLM-L6-v2


ChromaDB stores the document chunks, generated embeddings, and associated metadata.

The vector database is initialized as follows:

python
client = chromadb.PersistentClient(path="chroma_db")

collection = client.get_or_create_collection(
    name="documents_collection",
    embedding_function=sentence_transformer_ef
)


Each chunk is stored with metadata containing:

Source document name
Chunk index
Unique chunk ID
4. Semantic Search

When a user submits a question, ChromaDB retrieves the most relevant document chunks based on semantic similarity.

python
results = semantic_search(
    collection,
    query="What is YOLOv7?",
    n_results=5
)

5. Context Preparation

The retrieved chunks are combined into a single context, and their source information is collected.

The get_context_with_sources() function returns:

Retrieved context
List of source documents and chunk numbers
6. Answer Generation

The retrieved context and user's question are passed to the LLM through OpenRouter.

The system prompt instructs the model to answer only using the provided context.

If the answer is unavailable, the model is instructed to respond:

text
I don't know based on the provided documents.


The generated answer and its corresponding sources are then displayed.

💻 Usage
Step 1: Load a Document
python
file_path = "/content/yolov7_paper.pdf"

text = read_document(file_path)

Step 2: Process the Document
python
ids, chunks, metadatas = process_document(file_path)

Step 3: Store Chunks in ChromaDB
python
collection.add(
    documents=chunks,
    metadatas=metadatas,
    ids=ids
)

Step 4: Perform Semantic Search
python
query = "What is YOLOv7?"

context, sources = ask(
    collection,
    query,
    n_results=5
)

Step 5: Generate an AI Answer
python
query = "Provide me an introduction to YOLOv7."

answer, sources = rag_answer(
    collection,
    query,
    n_results=5
)

🧪 Example Queries

The following questions can be used to test the system with the YOLOv7 research paper:

python
rag_answer(
    collection,
    "What is YOLOv7?",
    n_results=2
)

rag_answer(
    collection,
    "YOLOv7 outperforms which models?",
    n_results=2
)

rag_answer(
    collection,
    "Provide me an introduction to YOLOv7.",
    n_results=2
)

rag_answer(
    collection,
    "What are Extended Efficient Layer Aggregation Networks?",
    n_results=5
)

📁 Project Structure
text
document-rag/
│
├── chroma_db/              # Persistent ChromaDB storage
│
├── documents/              # Input documents
│   └── yolov7_paper.pdf
│
├── main.py                 # Main application code
│
├── requirements.txt        # Project dependencies
│
├── .env                    # API key configuration (not committed)
│
├── .gitignore              # Files excluded from Git
│
└── README.md               # Project documentation


Note: The structure above is a suggested organization for converting the notebook into a standalone Python project.