
STREAM
In programming, streaming refers to processing data as it is being received, rather than waiting for the entire data set to be available. Think of it like watching a video online (streaming) instead of downloading the entire video before watching.
WHY STREAMING IS USEFUL:
Real-time experience: You can display parts of the response to the user as soon as they are ready, which feels faster.
Memory efficiency: It's better for large responses, as you don't need to wait for or store the full response in memory before working with it.
Continuous processing: You can process each chunk of data as it arrives.
A SIMPLE ANALOGY:
Imagine you're waiting for a friend to tell you a story. If they send you the whole story in one long text, you have to wait until they finish writing before you can start reading. But if they send you the story sentence by sentence, you can start reading immediately.
In Python, a streaming request looks like:
from openai import OpenAI
client = OpenAI()
stream = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Say this is a test"}],
    stream=True,
)
for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="")

FUNCTION/TOOLS CALLING
Connect models to external data and systems.
Function calling enables developers to connect language models to external data and systems. You can define a set of functions as tools that the model has access to, and it can use them when appropriate based on the conversation history. You can then execute those functions on the application side, and provide results back to the model.

OVERVIEW:

Many applications require models to call custom functions to trigger actions within the application or interact with external systems.
Here is how you can define a function as a tool for the model to use:
from openai import OpenAI
client = OpenAI()
tools = [
  {
      "type": "function",
      "function": {
          "name": "get_weather",
          "parameters": {
              "type": "object",
              "properties": {
                  "location": {"type": "string"}
              },
          },
      },
  }
]
completion = client.chat.completions.create(
  model="gpt-4o",
  messages=[{"role": "user", "content": "What's the weather like in Paris today?"}],
  tools=tools,
)
print(completion.choices[0].message.tool_calls)


This will return a function call that looks like this:


[
  {
    "id": "call_12345xyz",
    "type": "function",
    "function": { "name": "get_weather", "arguments": "{'location':'Paris'}" }
  }


Here are a few examples where function calling can be useful:
Fetching data: enable a conversational assistant to retrieve data from internal systems before responding to the user.


Taking action: allow an assistant to trigger actions based on the conversation, like scheduling meetings or initiating order returns.


Building rich workflows: allow assistants to execute multi-step workflows, like data extraction pipelines or content personalization.


Interacting with Application UIs: use function calls to update the user interface based on user input, like rendering a pin on a map or navigating a website.
THE LIFECYCLE OF A FUNCTION CALL:


When you use the OpenAI API with function calling, the model never actually executes functions itself - instead, it simply generates parameters that can be used to call your function. You are then responsible for handling how the function is executed in your code.





NUCLEUS SAMPLING
Nucleus sampling (also known as top-p sampling) is a technique used in language models (like GPT) to generate text. It controls randomness in the output by selecting words based on their probabilities but focuses only on the most likely ones.
HOW IT WORKS:

Probabilities of Words: When a language model generates text, it predicts the likelihood (probability) of each possible next word. For example:

"The cat is on the ..."  
Probabilities: 
- mat: 0.5  
- roof: 0.3  
- table: 0.1  
- moon: 0.05  
- etc.
Nucleus Sampling Mechanism:
The model doesn’t just pick the word with the highest probability (greedy decoding) or randomly choose any word (pure sampling).
Instead, it selects from the smallest group of words whose combined probabilities add up to a threshold value, called p (e.g., p=0.9).
Example:
For p=0.9:
It sums probabilities:
0.5 (mat) + 0.3 (roof) = 0.8
0.1 (table) = 0.9.
Words like "mat," "roof," and "table" are included in the "nucleus" (top-p set). Words like "moon" are ignored because they fall outside the top-p threshold.
Choosing a Word: After the nucleus is formed, the model randomly picks a word only from this set, using their probabilities to weigh the choice.
WHY USE NUCLEUS SAMPLING?
Balancing Creativity and Coherence:
Purely choosing the highest-probability word might make the text repetitive or predictable.
Random sampling without limits might create nonsensical or unrelated outputs.
Nucleus sampling strikes a balance: It maintains coherence while allowing for creative, less predictable results.
Controlling Diversity:
By adjusting the p-value:
A smaller p (e.g., p=0.7) focuses on only the most likely words, making the text more predictable.
A larger p (e.g., p=0.95) allows for more diverse choices, making the text more varied or imaginative.
A SIMPLE ANALOGY:
Imagine you're choosing a snack from a store:
Greedy Decoding: Always pick the most popular snack.
Random Sampling: Pick any snack, no matter how unpopular.
Nucleus Sampling: Look at the top snacks that most people like (e.g., top 90%) and pick one randomly from those.
This way, you’re likely to pick a popular snack but still leave room for variety.

TEMPERATURE
The temperature parameter controls the randomness or creativity in the AI’s responses. It determines how likely the model is to choose less probable words when generating text. Think of it as a "creativity dial." A lower value means the model is more deterministic and focused, while a higher value means it is more diverse and unpredictable.
HOW IT WORKS:
Low temperature (e.g., 0.1): The model generates predictable and logical outputs. It primarily sticks to the most probable next words, making the responses more focused and consistent.
High temperature (e.g., 0.9): The model generates more diverse and creative responses by considering a wider range of possible words. This can result in more imaginative but sometimes less accurate outputs.
EXAMPLES:
Low temperature (≤0.2): Prompt: "Write a professional email response to a job offer." AI Response: "Thank you for the opportunity. I am excited to accept the position and look forward to contributing to your team."
The response is clear, professional, and to the point.
High temperature (≥0.8): Prompt: "Write a professional email response to a job offer." AI Response: "Wow! I am beyond thrilled to receive this opportunity and cannot wait to start contributing my skills and passion to your amazing team. Let’s make great things happen together!"
The response is more enthusiastic and creative but less formal.
A SIMPLE ANALOGY:
Imagine ordering pizza:
Low temperature: You always order your favorite pizza with no variation (e.g., pepperoni). It’s reliable and predictable.
High temperature: You’re open to trying new toppings, experimenting with flavors, and even mixing things like pineapple and jalapeños. It might be exciting but can sometimes result in unexpected combinations.

MODEL
The model parameter specifies which version of OpenAI’s language model to use. Each model is trained to understand and generate human-like text, but their capabilities, speed, and cost vary.
EXAMPLE OF MODELS:
gpt-3.5 or gpt-4: These are general-purpose models with varying degrees of sophistication and context understanding.
gpt-4-turbo: A faster and more cost-effective version of GPT-4, ideal for many applications.
Custom models: Sometimes developers train or fine-tune models for specific tasks, like analyzing medical reports or generating code.
HOW IT WORKS:
The chosen model processes the input data (your prompts) and predicts the most appropriate outputs based on its training.
Advanced models (like GPT-4) can understand complex contexts, follow detailed instructions, and produce higher-quality text compared to simpler models.
EXAMPLES:
Using GPT-4: Prompt: "Explain the laws of thermodynamics in simple terms." Response: "The laws of thermodynamics describe how energy works in the universe: energy can’t be created or destroyed, everything becomes less organized over time, and heat moves from hot things to cold things."
Using a simpler model: Prompt: "Explain the laws of thermodynamics in simple terms." Response: "Thermodynamics is about energy. Energy is constant, becomes messy, and heat spreads."
The simpler model gives a less detailed and slightly less polished response.
A SIMPLE ANALOGY:
Choosing a model is like selecting a chef for your restaurant:
Basic model (e.g., GPT-3): A decent cook who can make basic dishes reliably.
Advanced model (e.g., GPT-4): A gourmet chef who can create intricate, high-quality meals.
Custom model: A chef specializing in a specific cuisine (e.g., sushi, Italian).

MESSAGES
The messages parameter defines the conversation history between the user and the AI. It helps the AI understand the context and respond appropriately. Each message consists of:
Role: Specifies the speaker (“system,” “user,” or “assistant”).
Content: The actual text of the message.
ROLES IN MESSAGES:
System: Provides instructions for the AI’s behavior. For example: "You are a helpful assistant."
User: Represents input from the person interacting with the AI. For example: "What is the capital of France?"
Assistant: Represents responses from the AI. For example: "The capital of France is Paris."
HOW IT WORKS:
The AI uses the sequence of messages to maintain context. It "remembers" the conversation based on the provided history.


EXAMPLES:
Single-turn conversation:
messages = [
    {"role": "user", "content": "What’s 2 + 2?"}
]
Response: "2 + 2 is 4."
Multi-turn conversation:
messages = [
    {"role": "system", "content": "You are a math tutor."},
    {"role": "user", "content": "What’s 5 + 7?"},
    {"role": "assistant", "content": "5 + 7 is 12."},
    {"role": "user", "content": "What about 12 x 3?"}
]
Response: "12 x 3 is 36."
A SIMPLE ANALOGY:
Messages are like a chat log between you and a friend. The AI reads the whole log to understand the conversation and give a meaningful reply.
MAX TOKENS
The max_tokens parameter limits the length of the AI’s response. A token is a chunk of text, roughly equivalent to a word or part of a word.
HOW IT WORKS:
If max_tokens is set to 100, the AI’s response will not exceed 100 tokens.
Shorter limits ensure concise responses, while longer limits allow more detailed outputs.
EXAMPLES:
Low max tokens (≤30): Prompt: "Explain photosynthesis briefly." Response: "Plants make food using sunlight."
High max tokens (≥200): Prompt: "Explain photosynthesis in detail." Response: "Photosynthesis is a process where plants use sunlight, water, and carbon dioxide to create oxygen and energy in the form of glucose. Chlorophyll in the leaves absorbs sunlight, initiating chemical reactions that sustain life on Earth."
A SIMPLE ANALOGY:
Imagine asking someone a question:
Low max tokens: They answer in one sentence (brief and quick).
High max tokens: They provide a detailed explanation with examples.

RESPONSES (n)
The n parameter specifies how many responses the AI generates for a single input. This gives you multiple options to choose from.
HOW IT WORKS:
If n=1, the AI generates one response.
If n=2, it generates two different responses, providing variety.
EXAMPLES:
Single response (n=1): Prompt: "Give me a motivational quote." Response: "Believe in yourself and all that you are."
Multiple responses (n=2): Prompt: "Give me a motivational quote." Response 1: "The best way to predict the future is to create it." Response 2: "Success is not final, failure is not fatal: It is the courage to continue that counts."
A SIMPLE ANALOGY:
Imagine asking a friend for advice:
Single response (n=1): They give you one suggestion.
Multiple responses (n=2): They provide two different suggestions so you can pick the one that resonates most.

