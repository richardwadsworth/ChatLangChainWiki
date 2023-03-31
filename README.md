# 🦜️🔗🔗 ChatLangChainWiki

This repo is an fork of https://github.com/hwchase17/chat-langchain/.  It is an implementation of a locally hosted chatbot specifically focused on question answering over Atlassian Confluence wiki content.  The significant change is the inclusion of a new document loader capable of parsing Confluence wiki pages as HTML.

Built with [LangChain](https://github.com/hwchase17/langchain/) and [FastAPI](https://fastapi.tiangolo.com/).


The app leverages LangChain's streaming support and async API to update the page in real time for multiple users.

## ✅ Running locally
1. Install dependencies: `pip install -r requirements.txt`
1. Create an [OpenAI API key](https://platform.openai.com/account/api-keys) and save the key to a new .env file in the project root folder in the format OPENAI_API_KEY=XXXX.
1. [Manaully export the Confluence wiki pages as HTML]((https://support.atlassian.com/confluence-cloud/docs/export-content-to-word-pdf-html-and-xml/)) to a folder called 'confluence_docs' in the root directory of this project.  Note you can bulk export a space.
   1. You can use other [Document Loaders](https://langchain.readthedocs.io/en/latest/modules/document_loaders.html) to load your own data into the vectorstore.
1. Run the app: `make start`
   1. To enable tracing, make sure `langchain-server` is running locally and pass `tracing=True` to `get_chain` in `main.py`. You can find more documentation [here](https://langchain.readthedocs.io/en/latest/tracing.html).
1. Open [localhost:9000](http://localhost:9000) in your browser.

## 🚀 Important Links

Refer to https://github.com/hwchase17/langchain/blob/master/README.md for information on deployed versions of the original code base and more documentation.

## 📚 Technical description

There are two components: ingestion and question-answering.

Ingestion has the following steps:

1. Manaully pull html from Confluence Wiki site
2. Load html with LangChain's [ReadTheDocs Loader](https://langchain.readthedocs.io/en/latest/modules/document_loaders/examples/readthedocs_documentation.html)
3. Split documents with LangChain's [TextSplitter](https://langchain.readthedocs.io/en/latest/modules/utils/combine_docs_examples/textsplitter.html)
4. Create a vectorstore of embeddings, using LangChain's [vectorstore wrapper](https://langchain.readthedocs.io/en/latest/modules/utils/combine_docs_examples/vectorstores.html) (with OpenAI's embeddings and FAISS vectorstore).

Question-Answering has the following steps, all handled by [ChatVectorDBChain](https://langchain.readthedocs.io/en/latest/modules/chains/combine_docs_examples/chat_vector_db.html):

1. Given the chat history and new user input, determine what a standalone question would be (using GPT-3).
2. Given that standalone question, look up relevant documents from the vectorstore.
3. Pass the standalone question and relevant documents to GPT-3 to generate a final answer.
