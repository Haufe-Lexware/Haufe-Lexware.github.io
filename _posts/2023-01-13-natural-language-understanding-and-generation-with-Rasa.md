---
layout: post
title: Natural language understanding and generation with Rasa
subtitle: An overview about the Chatbot framework, it’s implementation and the resulting message flow
category: dev
tags: [nlg, nlu, ml]
author: Leon Kaltenbrunn
author_email: kaltenbrunnl@haufe.com
header-img: "images/natural-language-understanding-and-generation-with-Rasa/hero.png"
---

Natural language processing (NLP) is a rapidly growing field of artificial intelligence that enables computers to understand and generate human language. Rasa is an open-source chatbot framework that uses NLP to enable developers to build sophisticated conversational systems that can understand and respond to user inputs in a natural and intuitive way.

In this blog post, we will provide an overview of Rasa and its implementation, as well as a detailed look at the resulting message flow. We will also discuss some of the key features and benefits of using Rasa for natural language understanding and generation. By the end of this post, you will have a better understanding of how Rasa works and how it can help you build powerful chatbots that can engage and assist your users.

### Everything starts with the training

One of the key features of Rasa is its ability to train machine learning (ML) models to understand and generate natural language. To do this, Rasa uses a dataset of example conversations that are annotated with "intents" and "entities". Intents represent the underlying goal or intention of a user's utterance, while entities are specific pieces of information that are mentioned in the utterance, such as a person's name or a date.

To train the ML model, Rasa uses a technique called supervised learning, where the model is fed the annotated training data and learns to predict the correct intent and entities for new inputs. The training data is typically sourced from real-life conversations that have been collected and labeled by the developers.

Once the ML model is trained, it can be used to map new user inputs to the correct intent and entities. For example, if a user asks "What is the weather like today?", the model will recognize the intent of the question (i.e. to get information about the weather) and may extract the entity "today" as a reference to the current date. This information can then be used by the chatbot to generate a response that provides the requested information.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/intro.png){:style="width:110%"}

Overall, the training of the ML model is a crucial step in enabling Rasa chatbots to understand and generate natural language. By providing a large and diverse dataset of annotated examples, developers can train the model to accurately recognize a wide range of intents and entities, enabling it to handle a variety of conversational scenarios.

### Processing user messages with the NLU pipeline

As a user, you may be wondering how your messages are processed by Rasa. When a message is sent to a Rasa chatbot, it undergoes processing by the NLU pipeline, which is composed of a series of steps that aim to comprehend and extract key information from the message. To enhance your understanding of the NLU pipeline, an example pipeline will be presented to illustrate how a message is processed. It's worth noting that this example is not exhaustive and that there are many other options and possibilities for your NLU pipeline.

The first step of our example pipeline is the Whitespace Tokenizer, which is responsible for splitting your message into individual words or "tokens". This allows the chatbot to analyze your message at a more granular level and identify important elements, such as individual words or phrases.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/1.png){:style="width:110%"}

Next, your message is passed through the Count Vectors Featurizer, which converts the tokens into numerical values that can be processed by the ML model. This step is important because ML models are not able to work with raw text data, so the featurizer is used to transform the text into a form that the model can understand.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/2.png){:style="width:110%"}

After the Count Vectors Featurizer, your message is passed through the Language Model Featurizer, which adds contextual information to the numerical values. This step takes into account the words that appear before and after each token, as well as their relationship to each other, to provide a more complete representation of your message.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/3.png){:style="width:110%"}

The DIETClassifier is the final step in an NLU pipeline, and it is responsible for extracting intents and entities from a given message. It uses the information collected from the previous steps of the pipeline, to make predictions about the intent and entities present in the message.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/5.png){:style="width:110%"}

Overall, the NLU pipeline is an essential part of the Rasa chatbot, and it plays a key role in enabling the chatbot to understand and respond to your messages in a natural and intuitive way. By processing your message through a series of steps, the NLU pipeline is able to extract important information and use it to generate a relevant and helpful response.

### Natural language generation with the NLG component

Once the Rasa chatbot has identified the intent and entities in a user's message, it must generate a natural language response to send back to the user. This is where the natural language generation component of Rasa comes into play.

The NLG component is responsible for generating the final response that is sent to the user. It does this by interfacing with a backend system, such as a database or external API, to retrieve the correct response for the given intent and entities. Depending on the specific implementation of the chatbot, the NLG component may use a variety of techniques to generate the response, such as template-based generation or machine learning-based generation.

Once the appropriate response has been retrieved, it constructs a "message object" that contains the final response and any additional information that is required to deliver the message to the user. This will include the text of the response, as well as details about the formatting and layout of the message.

After the NLG component has generated the message object, it is passed back to the Rasa policies, which are responsible for sending the message to the user. The policies may perform additional processing on the message object, such as adding buttons or other interactive elements, before sending it to the user.

{:.center}
![]( /images/natural-language-understanding-and-generation-with-Rasa/6.png){:style="width:110%"}

Overall, the NLG component plays a crucial role in the natural language generation capabilities of Rasa chatbots. By interfacing with a backend system and generating a message object, it enables the chatbot to generate responses that are natural, relevant, and engaging for the user.

### Conclusion

In conclusion, Rasa is a powerful open-source chatbot framework that uses natural language processing (NLP) to enable developers to build sophisticated conversational systems. By training machine learning (ML) models on annotated data, Rasa chatbots can understand and respond to user inputs in a natural and intuitive way.

The Rasa NLU pipeline is an essential part of this process, as it is responsible for extracting important information from user messages and passing it to the DIETClassifier. The NLG component then generates a natural language response that is relevant to the user's message and sends it back to the user.

Overall, Rasa provides a versatile and user-friendly platform for building chatbots that can engage and assist users in a wide range of conversational scenarios. By leveraging the latest advances in NLP and ML, Rasa enables developers to create chatbots that are highly effective at understanding and generating natural language.