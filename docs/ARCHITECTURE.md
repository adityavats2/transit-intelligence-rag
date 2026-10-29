This file is intended to be a blueprint of the various parts of this product that I am building. 

There are a few core questions I aim to answer in this file: 
1. What is the idea behind the product at a high level?
   Transit Intelligence: In a city like Toronto, with dense neighborhoods, high public transit ridership, and record expansion of infrastructure, it's important to have evidence-backed answers to questions regarding the new Ontario Line project, its impacts before completion, and its impacts after completion when it pertains to Line 1 and Line 2 transfer interchanges. Using publicly available files from the Toronto Transit Commission, Metrolinx, the City of Toronto, and the Government of Ontario, I aim to deliver specific answers to questions. 
2. How is this product going to be built using RAG?
   This product is going to be built using Retrieval-Augmented Generation techniques with a Claude model as the LLM that makes the decision on which deterministic code to fetch to correctly answer the user's queries. Instead of hardcoding logic in code that returns errors when inputs don't match the expected inputs, natural user inputs will allow Claude's model to run and find the cluster in the vector database from which it believes the correct answer will come and give it to the user without hallucinating. 
3. What are the external API's and developer tools that I will need?
   The external API for the LLM will be from Claude. A vector database will be used, either ChromaDB or LanceDB, and will be containerized in Docker. 
4. What is the ingestion flow?
   The data ingestion pipeline will have multiple parts. The raw data will consist of PDF files and other document types from the institutions previously mentioned that offer information. Instead of naive chunking, I will use semantic chunking to pair with the vector database by creating chunks using natural breaks in official documents such as headings, subheadings, sections, chapters, etc. The goal is to write a deterministic script that parses the PDF by its structural headings, ensuring that a chunk represents a self-contained policy, budget item, or construction milestone rather than a random text block. 
5. What is the storage layer?
   The storage of the documents will be done using chunks. These chunks will be derived from source documents. The chunking strategies will start off as structural chunking. This means that chunks will be created based on natural interruptions such as headings, subheadings, tables, chapters, etc.  
6. What is the retrieval and routing layer?
   Because vector databases will be used, the retrieval that will happen when a user types in their question will be embedding the user input as a vector in the vector database and finding the closest relevant vectors that are similar in meaning, the LLM reasoning and creating an answer. There is a real possibility that it hallucinates and those are challenges that I anticipate will need incremental solving. 
7. What is the synthesis layer?
   Using Claude models, the synthesis layer, or rather the generation layer, is the final piece of the puzzle. It will generate an answer based on where the most relevant connections are made between vectors in the database and where that question will be. 
