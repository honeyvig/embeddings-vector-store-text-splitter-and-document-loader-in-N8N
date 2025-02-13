# embeddings-vector-store-text-splitter-and-document-loader-in-N8N
’m attempting to upload JSON data into Pinecone’s vector store using N8N’s workflow. However, the workflow takes an excessively long time to complete until it reaches the memory limit. Surprisingly, the JSON data, which comprises less than 2500 lines and occupies only 75kb, is causing this issue. I’ve configured a text splitter with a recursive approach of 2000 chunk size and an overlap of 20. Additionally, I’m utilizing large embeddings from OpenAI.
------
It sounds like you're running into memory and performance issues due to the way you're processing and uploading data to Pinecone via N8N. Here are a few potential areas to investigate and optimize your workflow to resolve the delays:
1. Text Splitting Efficiency:

    Recursive Text Splitter: Using a recursive approach with 2000 chunk size and 20 overlap may lead to excessive chunking, causing each chunk to contain a lot of repeated or redundant data. This could slow down processing and memory usage. Try testing with a more balanced chunk size, say 1000 tokens, and a smaller overlap like 10-15 tokens.
    Test Splitter Behavior: Make sure the splitting process itself is efficient. It might be worth testing the splitter in isolation (outside the N8N workflow) to ensure it’s working as expected.

2. Embedding Computation:

    Large Embeddings: Using large embeddings from OpenAI can also be memory-intensive, especially when processing large volumes of data. If you’re embedding a large number of chunks (even small ones), it can quickly accumulate memory usage. Try reducing the size of the chunks or using a smaller embedding model for testing.
    Batch Embedding: Instead of embedding each chunk individually, try batching them together to make the process more efficient. OpenAI's API supports batch processing, and by embedding multiple chunks at once, you could reduce the overhead per request.

3. Memory Management:

    Limit Memory Usage: Check N8N's memory limits and consider adjusting them if possible. It might also be helpful to monitor memory usage during workflow execution. You can potentially introduce memory limits in N8N to prevent excessive memory consumption that leads to crashes or delays.
    Clear Cache: If N8N is caching intermediate results, that could be taking up memory. Ensure you're clearing or limiting caching during your workflow.

4. Parallelizing Workflow Steps:

    Parallel Processing: Depending on your N8N setup, you could parallelize the text splitting or embedding steps by breaking the JSON data into smaller batches and processing them in parallel. This way, each batch would consume less memory and be processed faster.

5. Check Pinecone Data Upload:

    Pinecone API Limits: Make sure you are following Pinecone's API rate limits and optimal data formats. If you're uploading data one chunk at a time, it may slow down, especially when you are handling a lot of smaller requests. Try batching the Pinecone insert requests to improve throughput.
    Optimize Vector Inserts: Pinecone also offers a method to upload vectors in bulk, which could save time and resources. Ensure that the data format is correct for Pinecone's expectations, and try sending them in larger batches.

6. Monitor Logs and Debug:

    Log Intermediate Steps: Log intermediate results at each step of the workflow to see where the performance is getting bottlenecked. Look for steps that take an unusually long time to execute, and focus on optimizing them.
    Pinecone Status: Check Pinecone's dashboard or logs for any indication of issues like throttling, failures, or high latency.

7. N8N Workflow Adjustments:

    Optimize N8N Execution: Review the workflow’s execution settings. For example, ensure that your N8N nodes are not performing redundant tasks and that you're properly using variables and expressions to reduce unnecessary computational overhead.

Proposed Solutions:

    Reduce chunk size: Lower the chunk size from 2000 to something smaller like 1000 tokens and use a smaller overlap (10-20 tokens).
    Embed in batches: Send batches of text chunks to OpenAI for embedding to reduce overhead.
    Bulk insert into Pinecone: After embedding, upload vectors to Pinecone in batches instead of one at a time.
    Check memory: Use profiling tools to track memory usage during the N8N workflow and adjust accordingly.
