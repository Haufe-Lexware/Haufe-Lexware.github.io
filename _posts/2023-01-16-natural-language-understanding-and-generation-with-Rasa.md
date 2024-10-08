---
layout: post
title: Natural language understanding and generation with Rasa
subtitle: An overview about the Chatbot framework, it’s implementation and the resulting message flow
category: dev
tags: [nlg, nlu, ml, gpt]
author: Leon Kaltenbrunn
author_email: kaltenbrunnl@haufe.com
header-img: "images/natural-language-understanding-and-generation-with-Rasa/hero.png"
---

Natural language processing (NLP) is a rapidly growing field of artificial intelligence that enables computers to understand and generate human language. One such example of NLP technology is GPT from [OpenAI](https://beta.openai.com/docs/models), which is a powerful language model that can generate human-like text. Rasa is an open-source chatbot framework that uses NLP to enable developers to build sophisticated conversational systems that can understand and respond to user inputs in a natural and intuitive way.

In this blog post, we will provide an overview of Rasa and its implementation, as well as a detailed look at the resulting message flow. We will also discuss some key features and benefits of using Rasa for natural language understanding and generation including how we can use Rasa in conjunction with the new [GPT models](https://beta.openai.com/docs/models). By the end of this post, you will have a better understanding of how Rasa works and how it can help you build powerful chatbots that can engage and assist your users like we do at Haufe with the [HRAssistant](https://www.haufe.de/hr/chatbot).

### Everything starts with the training

One of the key features of Rasa is its ability to train machine learning models to understand and generate natural language. To do this, Rasa uses a dataset of example conversations that are labeled with "intents" and "entities". Intents represent the underlying goal or intention of a user's utterance, while entities are specific pieces of information that are mentioned in the message, such as a person's name or a date.

To train the ML model, Rasa uses a technique called supervised learning, where the model is fed the labeled training data and learns to predict the correct intent and entities for new inputs. The training data should be sourced from real-life conversations to ensure high quality and is representative of what the model will encounter when it is in use. 

Once the ML model is trained, it can be used to map new user inputs to the correct intent and entities. For example, if a user asks "What is the weather like today?", the model will recognize the intent of the question (i.e. to get information about the weather) and may extract the entity "today" as a reference to the current date. This information can then be used by the chatbot to generate a response that provides the relevant information.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/intro.png){:style="width:100%"}

At Haufe, with the HR Assistant we provide our customers with the industry specific annotated training data that is crucial for a good model performance. While training your machine learning models keep in mind that the annotation and generation of high-quality training data is the most important part of creating a good model as it makes or breaks your users experience with your rasa chatbot.

### Processing user messages with the NLU pipeline

As a user, you may be wondering how your messages are processed by Rasa. When a message is sent to a Rasa chatbot, it undergoes processing by the NLU pipeline, which is composed of a series of steps that aim to comprehend and extract key information from the message. To enhance your understanding of the NLU pipeline, an example pipeline will be presented to illustrate how a message can be processed. It's worth noting that this example which is partly taken from our implementation at the HRAssistant is not exhaustive and that there are many other options and possibilities for your NLU pipeline.

The first step of our example pipeline is the `Whitespace Tokenizer`, which is responsible for splitting your message into individual words or "tokens". This allows the chatbot to analyze your message at a more granular level and identify important elements, such as individual words or phrases, that can then be set into context with others.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/1.png){:style="width:100%"}

Next, your message is passed through the `Count Vectors Featurizer`, which converts the previously extracted tokens into numerical values that can be processed by the ML model. This step is important because ML models are not able to work with raw text data, so the featurizer is used to transform the text into a form that the model can understand.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/2.png){:style="width:100%"}

After the `Count Vectors Featurizer`, your message is passed through the `Language Model Featurizer`, which adds contextual information to the numerical values. This step takes into account the words that appear before and after each token, as well as their relationship to each other, to provide a more complete representation of your message. Some example models that can be used as a `Language Model Featurizer` can be the [LaBSE Model](https://huggingface.co/rasa/LaBSE) by rasa or the new [GPT models](https://beta.openai.com/docs/models).

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/3.png){:style="width:100%"}

The `DIETClassifier` is the final step in an NLU pipeline, and it is responsible for extracting intents and entities from a given message. It uses the information collected from the previous steps of the pipeline, to make predictions about the intent and entities present in the message.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/5.png){:style="width:100%"}

### Natural language generation

Once the Rasa chatbot has identified the intent and entities in a user's message, it must generate a natural language response to send back to the user. This is where the natural language generation component of Rasa comes into play.

The NLG component is responsible for generating the final response that is sent to the user. It does this by interfacing with a backend system, such as a database or external API, to retrieve the correct response for the given intent and entities. Depending on the specific implementation of the chatbot, the NLG component may use a variety of techniques to generate the response, such as template-based generation or machine learning-based generation like for example the [GPT models](https://beta.openai.com/docs/models). At the HR Assistant we use GraphQL backend which is connected to an external API`s for translation called [DeepL](https://www.deepl.com/translator) and a [Postgres](https://www.postgresql.org/) database for content management and caching.

Once the appropriate response has been retrieved, it constructs a "message object" that contains the final response and any additional information that is required to deliver the message to the user. This will include the text of the response, as well as details about the formatting and layout of the message.

After the NLG component has generated the message object, it is passed to the Output Channel, which is responsible for sending the message to the user. The Output Channel may perform additional processing on the message object, such as adding buttons or other interactive elements, before sending it to the user. At the HR Assistant we provide two different output channel for our customers. The first one is MS Teams and the second is a custom-made web-widget that can be embedded into any HTML based application or website. See the [Rasa docs](https://rasa.com/docs/rasa/next/connectors/custom-connectors) for all custom connectors. 

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/6.png){:style="width:100%"}


### Conclusion

In conclusion, Rasa is a powerful chatbot framework that uses natural language processing to enable developers to build sophisticated conversational systems. By training machine learning models on labeled data, Rasa chatbots can understand and respond to user inputs in a natural and intuitive way. 

The essential part of this is the Rasa NLU pipeline, as it is responsible for extracting important information from user messages and passing it to the `DIETClassifier`. The NLG component then generates a natural language response that fits to the user's message and sends it back resulting in a natural conversation flow.

Overall, Rasa provides a versatile platform for building chatbots that can engage and assist your customers in a wide range of conversational scenarios. By leveraging the latest advances in NLP and ML, Rasa enables you to create a chatbot that is highly effective at understanding and generating natural language.

At Haufe with the HRAssistant we utilize the rasa framework to generate a highly scalable Assistant that can be implemented by our customers to serve their users. Advances like the [GPT models](https://beta.openai.com/docs/models) will improve this even further as we're already planning on implementing the powerful language model in our product to further increase the value for our customers. 

Thanks for reading!

#### Further Reading & Sources
- [Rasa](https://rasa.com/)
- [Rasa's documentation on training data](https://rasa.com/docs/rasa/tuning-your-model/)
- [Rasa's documentation on the NLU pipeline](https://rasa.com/docs/rasa/nlu/components/)
- [Rasa's documentation on handling business logic](https://rasa.com/docs/rasa/nlu/components/)
- [Haufe HR Assistant](https://www.haufe.de/hr/chatbot)
