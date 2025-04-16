# Scott's AI Model Collection

This repository contains a collection of hosted AI models I find differentuated and useful for building Agentic AI applications.  I host on [Runpod](https://www.runpod.io) or [Modal](https://modal.com) and the models deployed to those providers are configured  utilize the full allowed context length without any rate limiting constraints.

Unlike ALL commercial API providers that impose various limits even after payment, these models are configured without:

- RPM (Requests per minute) limits
- RPD (Requests per day) limits
- TPM (Tokens per minute) limits
- TPD (Tokens per day) limits

This makes the my hosted models ideal where predictable performance is essential.  There is nothing more frusterating to get rate limited just when you get your agentic application to a point where its functioning as designed.

I also am including models from various providers that I use on a regular basis that have reasonable limits.  The detail of those limits are outlined in the table below.

You may ask, why not any hosting from Azure, GCP, or AWS.  Well the simple answer is they charge a fortune for their GPU's and I am not made of money.  If that doesn't make sense to you, I highly suggest you spend some time with this [Kelsey Hightower](https://github.com/kelseyhightower/nocode) project before you try to write any AI apps.

## Model Specifications

The table below provides detailed specifications for each model in this collection:

| Model Name | ConfigID | Provider | Engine | Tool Calling | Reasoning | FlashInfer | Quantization | Context Window | GPU Type | GPU Count | RPM | RPH | RPD | Cache | LB |
|------------|----------|----------|------------------|--------------|-----------|------------|--------------|----------------|----------|---------|-----|-----|-----|-------|----|
| [Granite-3.2-8b-instruct](#granite-32-8b-instruct) | pc-modal-c07335 | [Modal](https://modal.com) | VLLM 0.8.3 v1 | Yes | No | 0.2.5 | None | 128K | L40S | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [DeepHermes-3-Mistral-24B-Preview](#deephermes-3-mistral-24b-preview) | pc-model-08b0cd | [Modal](https://modal.com) | VLLM 0.8.2 v0 | No | Yes*1 | 0.2.0.post2 | None | 32K | A100-80GB | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [Qwen2.5-Coder-32B-Instruct](#qwen25-coder-32b-instruct) | pc-modal-467df0 | [Modal](https://modal.com) | VLLM 0.8.3 v1 | Yes | No | 0.2.0.post2 | GPTQ-Int4 (gptq_marlin) | 32K | A100-40GB | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [QwQ-32B-AWQ](#qwq-32b-awq) | pc-modal-19305a | [Modal](https://modal.com) | VLLM 0.8.2 v0 | Yes | Yes | 0.2.0.post2 | AWQ | 32K | L40S | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [DeepSeek-R1-Distill-Llama-8B](#deepseek-r1-distill-llama-8b) | pc-modal-7b241d | [Modal](https://modal.com) | VLLM 0.8.3 v0 | No | Yes | 0.2.0post2 | None | 32K | L4 | 2 | Unlimited | Unlimited | Unlimited | None | None |
| [BGE-large-en-v1.5](#bge-large-en-v15) | pc-modal-1a7579 | [Modal](https://modal.com) | VLLM 0.8.3 v0 | N/A | N/A | N/A | None | N/A | T4 | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [Jina Reranker V2 Base Multilingual](#jina-reranker-v2-base-multilingual) | pc-modal-b951df | [Modal](https://modal.com) | VLLM 0.8.3 v0 | N/A | N/A | N/A | None | N/A | L4 | 1 | Unlimited | Unlimited | Unlimited | None | None |
| [DeepSeek R1 Distill Llama 70b](#deepseek-r1-distill-llama-70b) | pc-groq-d-6b723c | [Groq](https://www.groq.com) | N/A | No | Yes | N/A | Unknown | 32K | N/A | N/A | 30 | N/A | 1000 | None | None |

*1 - Reasoning is toggled via the prompt


## Deployment

The models I host are deployed on a containerized serverless capability. To save on costs, each model will scale to zero when not in use.  If a model has scaled down, it will take a few minutes for the model and associated container to cold start on fresh use.  If you want to warm up the model before use, you can send a POST to the /docs or /health endpoint which will warm up the model and associated container.  Containers currently run for 10 minutes without an active call before scaling down.  In my experience, as you are interating on code, you usually execute, think for 3 to 6 minutes, modify code, and execute again so 10 minutes is more than enough time to keep the container warm and keep you from repeated coldstart.

The models I have deployed are great for batch AI or agentic applications.  They can also be used for real time application with the disclaimer of the container start time.  Most models under 14B will start in 3 minutes or less.  As the models grow in size, so does the container start time so be aware.

Keep in mind that I store the models that run on [Modal](https://modal.com) on modal volumes because [Modal](https://modal.com) does not charge for that storage.  These models will cold start more quickly than on [Runpod](https://www.runpod.io).  Runpod likes to charge for that feature so I load the models in realtime from Huggingface which introduces an additional cold start delay.  In addition, in some examples where I have decided to use then TensorRT-LLM engine, those models are compiled directly into the container image, so of course those will start more quickly.

## Testing

Each model has been painstakenly tested and deployed for the best price and performance GPU configuration.  You will notice by looking at the various hugging face repo's that some of these models might memorywise fit on a single GPU, but are deployed with multiple GPUs.  This is because I wasn't satisfied with the single GPU inference response times.  You might also notice some smaller models hosted on GPU's that seem overpowered to run the model.  Again, this is because I wasn't satisfied with the TPS performance or some other factor that influenced me to step up in GPU size.  In addition, with the models I host, there are no advanced Nvidia features in use (mig, timeslicing, and so on), I found after wasting hundreds of hours the most performant delivery of a model is always exclusive GPU(s) attached per container.

## Performance

The models I host are host on [Modal](https://modal.com) run in either AWS and GCP.  I don't have any control over the hyperscaler regions, but from my testing they all deploy to domestic endpoints.  The models I host on [Runpod](https://www.runpod.io) execute on [Runpod](https://www.runpod.io) hardware in their secure cloud, rather than the community cloud, and are hosted domestically.  With that said, both providers will scale to thousands of GPU's and containers if you apply pressure to the endpoint.  Of course I ask you to be responsible as we all know GPU's are not free and the GPU price per ounce costs are more expensive than gold.  I do have budgets in place if a mistake is made and you scale up 10k containers so no need to worry about that on your end.

## Security

I originally exposed the raw model endpoints, but I have since interjected an AI gatway layer in the form of Portkey.  The main reason I have done this is to add model routing and load balancing, model fallbacks, caching, and security features to some of the models I host.  My original idea was to host the gateway myself in the form of Litellm or Kong AI Gateway, I have used both gateways extensively, but I decided that I didn't want the care and feeding of a production level kube cluster and deal with all that goes along with hosting those gateways by hand.  You will see in the model table that some of the models I host have a LB (Load Balancing) and Cache feature enabled.  Those are portkey configurations, and have nothing to do with how the models are deployed for inference.

It is stupid simple to inject call backs and various configurations into model hosting to log requests and responses.  You have my word that I don't do any of that with the models I host.  For the models that are delivered via an API provider like [Sambanova](https://sambanova.ai), I have no control over what they do with your data.  As I add providers over time, if the provider offers the ability to disable caching and logging I will do that, but in the case of [Sambanova](https://sambanova.ai), I have no control over what they do with your data.  Buyer Beware.

Given that everything goes thru Portkey, you need to be aware that your requests and responses are being logged unless you follow this guide https://portkey.ai/docs/product/observability/logs#do-not-track.  Of course any model that is configured for caching is going to cache your requests and responses regardless of what you do with your client side settings so buyer beware.

Lastly, I am sure someone will commit their API key to github.  When that happens and my watchdog service catches it, the API key will be rotated and the old key will be disabled.  If for some reason you can't access the services, its because I am a poor programmer and the key was rotated via the watchdog by mistake.  If that happens, just reach out to me and I'll get you the new key.  Hopefully that wont happen often :)

## Cost

All of this goodness comes at an expense which I am covering for you.  Use it freely.  I would rather have knowledgable architects that are thinking, building, and testing new ideas, frameworks, and tech than to have you skip the realization of your ideas because its a pain to have to deal with spreading your credit card all over the internet.  I may put a donation link here eventually, but for now, I would rather just cover the costs to see what you build!

## A note from the developer

Developing AI applications can be frusterating.  I found developing agentic AI apps over the past year using local models via Ollama or LMStudio often relegated me to make concessions on quantization levels or dealing with the limitations of not having the ability to scale.  Plus developing on the desktop is slow.  In addition, I found dealing with the API providers was rate limited, expensive, and very frusterating when I didn't have complete control over the model, the engine it was running, and its configuration.  But there is a happy medium.  Some of what I have built uses the models I host for the heavy lifting, and when I need an LLM as a judge or to do some kind of evaluation I'll use an API provider.

## Usage

Once deployed, you can interact with the models using the OpenAI Python client library or any HTTP client that supports the OpenAI API format. Authentication is handled via API keys.


Example:
```python
# pip install portkey_ai
from portkey_ai import Portkey
# Construct a client with a virtual key
portkey = Portkey(
  api_key="{INSERTHEKEYIGAVEYOUHERE}",
  #virtual_key="openai-test1-accd5d",
  config="{INSERTTHECONFIGFROMTHEMODELTABLEHERE}"
)
completion = portkey.chat.completions.create(
  messages = [{ "role": 'user', "content": 'Why does my dog like to lick his butt' }])
print(completion)
```

## Roadmap

I plan to add more models to this collection over time.  If you have a model you'd like me to add, please let me know.

1. ✅ Deepseek R1 Distill model - Added!
2. ✅ BGE-large-en-v1.5 - Added!
3. ✅ Qwen2.5-Coder-32B-Instruct - Added!
4. ✅ QwQ-32B-AWQ - Added!
5. ✅ DeepSeek-R1-Distill-Llama-8B - Added!
6. ✅ Jina Reranker V2 Base Multilingual - Added!
7. ✅ DeepSeek R1 Distill Llama 70b - Added!
8. [Sambanova](https://sambanova.ai) models
9. [Runpod](https://www.runpod.io) deployed models
10. Load balancing and Caching examples
11. Baseten hosting
12. Bedrock models
13. Same model deployed in same configuration with VLLM, TensorRT, and SGLang for performance testing.


## Model Notes

### Granite-32-8b-instruct

**ConfigID**: `pc-modal-c07335`

**Model Card**: [IBM/granite-3.2-8b-instruct](https://huggingface.co/ibm-granite/granite-3.2-8b-instruct)

The Granite-3.2-8b-instruct model is IBM's 8B parameter instruction-tuned model that excels at following instructions and tool calling.

I use this model quite a bit for tool calling because its one of the few tool calling models that works at 8b parameters and its cheap to host.  The llama and qwen model family has struggled for me with tool calling in the smaller versions.

### DeepHermes-3-Mistral-24B-Preview

**ConfigID**: `pc-model-08b0cd`

**Model Card**: [NousResearch/DeepHermes-3-Mistral-24B-Preview](https://huggingface.co/NousResearch/DeepHermes-3-Mistral-24B-Preview)

The DeepHermes-3-Mistral-24B-Preview model is NousResearch's 24B parameter model based on Mistral architecture. Key features include:

- High-quality general purpose model with strong reasoning capabilities

What is compelling to me is this model is trained as the first LLM model to unify both "intuitive", traditional mode responses and long chain of thought reasoning responses into a single model, toggled by a system prompt.

Add this exact content to the start of your system prompt and the model will act as a thinking model:

You are a deep thinking AI, you may use extremely long chains of thought to deeply consider the problem and deliberate with yourself via systematic reasoning processes to help come to a correct solution prior to answering. You should enclose your thoughts and internal monologue inside <think> </think> tags, and then provide your solution or response to the problem.

Do not include that section in your system prompt and it will act as a traditional model.

### Qwen2.5-Coder-32B-Instruct

**ConfigID**: `pc-modal-467df0`

**Model Card**: [Qwen/Qwen2.5-Coder-32B-Instruct](https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct)

The Qwen2.5-Coder-32B-Instruct model is Alibaba's code-specialized large language model with 32B parameters. This version uses GPTQ 4-bit quantization to reduce memory requirements while maintaining high performance.

This model I have found works fantastic as a coding replacement model for Sonnet 3.5-7.  If you are into vibe coding with technologies such as Aider, Roo Code, or Cline, you'll find its near impossible to find a model that works as well as Sonnet 3.5 or 3.7.  I have tested at least 20 different model architectures and parameter sizes, and Qwen25-Coder-32B-Instruct always comes out on top.

I also have found that the int4 awq quantized version works just as well as full precision.  So in order to save on model hosting costs and fit it onto a 40gb card, I run it with awq quantization.  No need to spend extra pennies when not required.  I did run this for a moment on an L40S rather than the A100-40GB, but the performance was subpar so I reverted back to A100-40GB.  Quite honestly when used for vibe coding, it probably could use a bit more oomph than the A100 but I am cheap and willing to wait a little longer to save a buck.

### QwQ-32B-AWQ

**ConfigID**: `pc-modal-19305a`

**Model Card**: [Qwen/QwQ-32B](https://huggingface.co/Qwen/QwQ-32B)

QwQ-32B-AWQ is Qwen's specialized reasoning model with 32B parameters, quantized using AWQ to 4-bit precision. This model is particularly strong at mathematical reasoning, scientific analysis, and complex problem-solving tasks.

What makes this model special is its dual capabilities - it combines both tool calling and reasoning in a single model. The reasoning is implemented using the DeepSeek-R1 reasoning parser, which allows the model to show its step-by-step thinking process before providing a final answer.

### DeepSeek-R1-Distill-Llama-8B

**ConfigID**: `pc-modal-7b241d`

**Model Card**: [unsloth/DeepSeek-R1-Distill-Llama-8B](https://huggingface.co/unsloth/DeepSeek-R1-Distill-Llama-8B)

The DeepSeek-R1-Distill-Llama-8B model is a distilled version of DeepSeek's R1 reasoning model, based on the Llama architecture with 8B parameters. This model is specifically designed to provide strong reasoning capabilities in a more efficient package.

Key features include:
- Enabled reasoning/thinking capabilities with the DeepSeek R1 reasoning parser
- 32K token context window
- Optimized for showing step-by-step thinking when prompted with reasoning questions

This model is particularly useful for applications requiring detailed reasoning processes while maintaining reasonable inference speeds and resource requirements. When prompted with "Please show your thinking" or similar instructions, the model will demonstrate its reasoning process before providing a final answer.  Sometimes it shows its work when you don't ask for it to do so which I find troublesome if what I have developed isn't setup to handle that information so know that you might get some unexpected results.

### BGE-large-en-v1.5

**ConfigID**: `pc-modal-1a7579`

**Model Card**: [BAAI/bge-large-en-v1.5](https://huggingface.co/BAAI/bge-large-en-v1.5)

The BGE-large-en-v1.5 model is a state-of-the-art embedding model developed by BAAI (Beijing Academy of Artificial Intelligence). This model is specifically designed to convert text into high-quality vector embeddings for various natural language processing tasks.

Key features include:
- 1024-dimensional dense vector embeddings
- Optimized for semantic search and text similarity tasks
- Supports English language content
- Deployed with vLLM's embedding capabilities for efficient inference

This model is particularly useful for:
- Building semantic search systems
- Document retrieval applications
- Text clustering and classification
- RAG (Retrieval-Augmented Generation) systems

Example usage involves sending text to the `/v1/embeddings` endpoint with a JSON payload containing an "input" field with your text, which returns vector embeddings that can be used for semantic search and similarity comparisons.

### Jina Reranker V2 Base Multilingual

**ConfigID**: `pc-modal-b951df`

**Model Card**: [jinaai/jina-reranker-v2-base-multilingual](https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual)

The Jina Reranker V2 Base Multilingual model is a powerful cross-encoder designed for reranking search results across multiple languages. This model evaluates the relevance between a query and document pairs, providing scores that can be used to reorder search results for improved precision.

Key features include:
- Multilingual support for over 100 languages
- Optimized for document reranking tasks
- Based on a cross-encoder architecture for direct relevance scoring
- Deployed with vLLM's scoring capabilities for efficient inference

This model is particularly useful for:
- Improving search result quality in multilingual applications
- Reranking candidate documents in RAG (Retrieval-Augmented Generation) systems
- Enhancing document retrieval precision in information retrieval systems
- Fine-tuning search results for domain-specific applications

Example usage involves sending a query and a list of documents to the `/v1/rerank` endpoint, which returns the documents reordered by relevance score.

### DeepSeek R1 Distill Llama 70b

**ConfigID**: `pc-groq-d-6b723c`

**Model Card**: You never know exactly which version or quant of a model an API provider delivers.

The DeepSeek R1 Distill Llama 70b is a distilled version of the Llama 70B model, optimized for efficient reasoning and throughput. Hosted on [Groq](https://www.groq.com), this model supports a 32K context window and is suitable for applications requiring strong reasoning capabilities without tool use.  When prompted with "Please show your thinking" or similar instructions, the model will demonstrate its reasoning process before providing a final answer.  Sometimes it shows its work when you don't ask for it to do so which I find troublesome if what I have developed isn't setup to handle that information so know that you might get some unexpected results.

I do not pay for [Groq](https://www.groq.com) so once you hit the limits of this model, you'll start to get errors.

Key features include:
- 32K context window
- Reasoning enabled
- Hosted on [Groq](https://www.groq.com) hardware for high throughput (30 RPM, 1000 RPD)

This model is ideal for tasks that benefit from extended context and detailed reasoning, such as multi-step problem solving, document analysis, and agentic workflows.  [Groq](https://www.groq.com) is blazing fast, must faster than anything I could deliver using Nvidia GPU's and I use it for tasks requiring that speed.
