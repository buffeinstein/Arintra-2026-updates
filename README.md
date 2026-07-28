# Arintra-2026-updates

Coming soon! Got to lock in for some interviews real quick and then I'll build this out 


You can see my rough notes below:


Sprint 1: Local Inference & Core Retrieval Pipeline
Ticket 1: Local Inference Engine Setup & Environment Configuration
Difficulty: 3/10
Agentic Technique Level: None (Basic Infrastructure)
2025 vs 2026: Identical. Pulling a GGUF file and serving it locally via LM Studio is the exact same process.
Recall the Arintra project was an experiment measuring the behavior of the open-source models available at the time, to see if they could do parts of their pipeline for free and increase the security of their pipeline (due to the sensitive nature of healthcare documents and the need for HIPAA compliance). 
This project was my first introduction to using LLMs, and I took notes throughout on what I was thinking and confused about and what my goals were in terms of the project vs personal learning. Reading back on these, not to my surprise, is like a reading a piece of ancient history -  so much of what I was doing then, thinking I was learning the cutting edge tools to use at my next job, is code and logic that I’ll likely never have to write again due to the progress of AI SDKs. So, with the permission of Arintra, I decided to go through the process and re-do some of it using 2026 tools to re-learn the concepts and also see how much easier it is with the progression of tools. And also show off my brain and multitude talents to future employers xD 
What does it mean to use an open-source model locally? 
Local just means that you’re not using an API which connects to a server that others can also connect to, but rather a server that is not open to the public.
To understand how we’re going to do this for this project, let us understand what a model is and how you run it, and how that motivates the layers of software between the hardware all the way up to the Python (or whatever top level language) you are writing your application logic in. 
Large Language Models are neural networks - billions of mathematical knobs that are dialed to digest and generate text accurately. When you’re downloading a model, you are literally just downloading these mathematical values, stored as vectors and arrays. Right after training these models (dialing the mathematical knobs), these values are stored as a high-precision 16-bit float, which means that each one of the billion dials requires exactly 16 bits to be stored. A small model is considered to have 5 - 10 billion parameters - which means many gigabytes to store the model. Thankfully, this aspect is easily solved by compressing the arrays, through a process called quantization that I don’t know too much about.  The amount of space you need here is static - you download the model and then it just stays there, doesn’t grow or shrink to run the model. 
Just one run of the LLM  (not to be confused with a call to the APIs that we normally call a model through, but rather a direct call to the LLM) looks like giving the model a string of words (the prompt) and then having it run through the neural network to give you exactly ONE MORE TOKEN. This makes parallelization of LLM work impossible, which means that the generation of each word will be as slow as the slowest part of a data pipeline - so the hardware has to be the fastest possible throughput for the full generation of the response to be at a reasonable speed for the user. Even the most basic API that was released to the public had to have this capability to create a coherent response and then responded over HTTP at the right time - ie making the model autoregressive and having it determine when to return. When we download a model locally, all we get is the ability for the model to generate one token at a time. 
So, based on those constraints, what are the layers of hardware/software configuration to get this downloaded model to actually work? The layers are: 
Layer
Generic Role
What it Does
Your 2026 Project Recommendation
1. Data Validation & Type Safety
Data Modeling & Validation
Defines the exact structure of inputs/outputs; auto-compiles Python schemas into JSON Schemas for network payload delivery.
Pydantic v2
2. Orchestration & Parsing
Application Logic
Reads raw documents, applies regex splitters, handles system prompt templates, and executes the HTTP REST API calls.
Python (FastAPI + Instructor SDK)
3. Networking / Transport
API Specification Protocol
Standardizes how parameters (temperature, messages, schemas) are formatted over raw HTTP REST payloads.
OpenAI API Specification
4. Local Inference Engine
Model Server / Runtime
Exposes the API port, runs the tokenizer, compiles JSON Schemas into Finite State Machines (FSMs), and manages the KV Cache.
Ollama (running headless inside Docker)
5. C++ Inference Engine
Low-Level Math Solver
The underlying highly-optimized C++ execution loops that handle the actual model architecture logic and memory mapping.
llama.cpp (packaged natively inside Ollama)
6. GBNF/FSM Compiler
Constrained Decoding Engine
Modifies the output token probabilities (logits) at runtime to mathematically guarantee the model outputs 100% syntactically valid JSON.
XGrammar or Outlines (integrated directly into Ollama's schema engine)
7. Parallel Computing API
Hardware Abstraction Platform
Translates high-level C++ math requests into highly optimized, parallelized computer kernels that GPUs can run.
NVIDIA CUDA Toolkit
8. System Driver
Hardware Controller
Low-level OS driver that handles VRAM memory allocation, temperature throttling, and schedules instruction sets to physical cores.
NVIDIA Proprietary Display Driver
9. Hardware Compute Layer
Physical Processor (Silicon)
The muscle. Performs high-bandwidth tensor matrix math over thousands of physical cores simultaneously.
NVIDIA L4 GPU (24GB VRAM) on Google Cloud Platform (GCP)


Let’s start from application layer, where the developer is writing code in Python with LLM libraries and making API calls to the LLM, all the way down to the hardware. The program is making the API call  - it must send JSON. So the program itself figures out what the JSON will be, ie what the prompt + libraries and all of that come together to make this JSON thing. ANd then that is sent over HTTP to Ollama

So the communication all the way down happens in JSON xz

So the application layer 



So - we need something that will create just this very basic loop for it - which is called the local inference engine. And also, load the model into the right hardware and create a port for us to connect to. For this, we need something like LM Studio or Ollama. Also, because this is doing an autoregressive thing where the tokens stored per run is getting bigger and bigger as the model does more loops, we’ll need a cache and dynamic memory.
So now we have the model and the inference engine that loads up the model in the hardware and exposes the port. The inference engine, however, is not what l 
This part has not changed  from 2025 to 2026. All of this is the same. But in 2026, we can break up the memory needed and the deployment process with much more ease. So we’re going to use this from GCP: https://cloud.google.com/blog/products/application-development/run-your-ai-inference-applications-on-cloud-run-with-nvidia-gpus

Ticket 2: Clinical Document Ingestion & Section Parsing Utilities
Difficulty: 3/10
Agentic Technique Level: None (Data Pre-processing)
2025 vs 2026: Identical. Standard Python regex and file-system traversal.
Ticket 3: NumPy Native Flat Vector Similarity Search Layer
Difficulty: 5/10
Agentic Technique Level: None (Algorithmic Math)
2025 vs 2026: Identical. The linear algebra for cosine similarity has not changed.
Sprint 2: Observability, Stress-Testing, and Scaled Inference
Ticket 4: Self-Hosted Telemetry Stack Infrastructure Deployment
Difficulty: 6/10
Agentic Technique Level: None (DevOps / Observability)
2025 vs 2026: Identical. Spinning up Docker containers for Langfuse and Postgres is standard MLOps in both years.
Ticket 5: Context Volume Stress-Testing & Attention-Dilution Profiling
Difficulty: 4/10
Agentic Technique Level: None (Benchmarking)
2025 vs 2026: Identical. Flooding the prompt to measure hallucination thresholds is a static testing procedure.
Ticket 6: Provider-Agnostic Engine Migration (The Groq Pivot)
Difficulty: 2/10
Agentic Technique Level: None (API Routing)
2025 vs 2026: Identical. Swapping a base URL and an API key is basic configuration management.
Sprint 3: Harness Engineering & Agentic Autonomy
Ticket 7: Tool-Calling & Iterative Search
Difficulty: 6/10
Agentic Technique Level: Intermediate
2025: Heavy prompt engineering forcing the model to output strict JSON strings, which your script then manually parsed and executed.
2026: Relying on native tool_use schemas integrated directly into the LLM API, abstracting away the string parsing entirely.
Ticket 8: Autonomous Error Recovery
Difficulty: 8/10
Agentic Technique Level: Advanced
2025: Writing raw while loops that intercept bad text outputs, manually append "Error: try again" text strings to the chat array, and force a retry.
2026: Using a structured harness where validation exceptions are caught and passed directly back to the model as standardized tool_result errors without custom control-flow loops.
Ticket 9: Reactive Context Compaction
Difficulty: 9/10
Agentic Technique Level: Advanced
2025: Hardcoding a token counter that brutally slices off the oldest array indexes or triggers a manual secondary API call to summarize the chat history when limits are hit.
2026: Implementing dynamic, multi-layer context compaction within the agent framework that seamlessly compresses background history while explicitly preserving active task states and tool results

