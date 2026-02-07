use this as a “typical” article that explains how to use AI [Artificial%20intelligence%20%28AI%29%20for%20small%20business%3A%208%20uses%20%7C%20QuickBooks](https://quickbooks.intuit.com/r/running-a-business/ai-for-small-business/)

use Airtable as a clue to how this might be marketed [https://www.airtable.com/platform/interface-designer](https://www.airtable.com/platform/interface-designer) \- use this to talk about tools, enterprise, and no code, low code… the data you have is the most important thing, and abstracting the data and having many options for how you access it might be the real power \-

Satya said enterprise is dead

* discuss schemas and what the implications of that are from this post [https://medium.com/@iamdavidchan/did-satya-nadelle-really-say-saas-is-dead-fa064f3d65d1](https://medium.com/@iamdavidchan/did-satya-nadelle-really-say-saas-is-dead-fa064f3d65d1)

*gm*

* CRUD… take out the D

# **The End of Traditional Applications**

## **Applications as Thin Layers**

According to Nadella, traditional business applications are essentially thin user interface (UI) layers over databases, designed to perform basic operations — create, read, update, and delete (CRUD).

These layers provide the logic that translates database interactions into actionable outputs. However, in the age of AI, the need for such intermediary layers is rapidly diminishing.

My take: and this is something you already learn when you work on a product that is almost entirely or entirely you think about how requirements look and they are often focused on use-cases built around a user \- makes sense \- a user is interested in shoes and does a search and lands on eCommerce site and the sites job is to present that brands’ aesthetic \- if a store, to show selection and options and prices \- in between maybe a store is curating brands \- so we try and explain the user journey as that customer does x, x1, in linear time and we make additional assumptions about what they learn along the way and how and why they make decisions and build features to optimize at each point of the decision tree to the users benefit balanced with the goals of the business \- ok … this all makes sense \- sessions based on chunks of time where we assume customer is doing something \- now, typically, the events outside of these discreet sessions are treated as events in an attribution model and it’s a model because it’s predictive and it’s dealing with much lower probability correlation \- we are much less confident about

(Data vs code idea… data you only have “what’s there” where in theory, code can create many many many perturbations of logic \+ interface with the same set of data \- so when you work backwards, you are constrained by the data, but relatively unconstrained by the code

## **Maybe its not just SaaS \- Ecommerce example**

So think about the PM again designing \- what if you started with the data and simply thought about the intersections \- instead of thinking about it in a 2D UI, you thought about it like a baysean decision tree \- the user and your system interact and each acquires new info, updates priors and makes a decision \- system on what to say/display/play media \- it’s a decision tree with two agents \- now, as the designer, my “raw materials” are the catalog of my product and all attributes, what I know about customer, what I’ve learned in this session (different), and what I “know” about the world outside based on an LLM or a fine-tuned LLM \- then you start thinking about what layers of the data big and small (this ties back to personalized personalization) \- “what I’ve learned this session” is like context window memory

The frustration with building data driven products if you do think data first is that the features or improvements almost always are related to new data sources you don’t have, new classifications and grouping of existing data, new aggregations, new or better predictive outputs, \- now this might not be as hard and the good thing is that you are lifting out of a UI and a linear timeline \- you are simply looking at snapshots of knowledge states between you and your customer, and it is easy to understand what data or data outputs would be required for an interaction, but the UI or the “process” the customer is going through can be abstracted away and the requirement is focused on their knowledge state (state of mind) which is closer to how a human would treat it \- what if all the data says a customer likes X but today, they are curious about Y and nothing so far would tell us that \- in a linear UI you only discover that through clunky direct methods like search or deep browse or indirectly because your personalization fails miserably \- but one quick “let’s do sometiing crazy\!” Would allow you to pivot and be like “oh yeah \- let’s\!” And you can then learn something like “I want to splurge” or “I know I always wear black \- I want color\!” And then the context isn’t just “colorful” it’s “people who normally wear black what do they do when they decide they want color?”

Example from career \- more data\!: music visualization \- I wanted to be able to go “in” (or something) for more popular or less popular, I wanted cluster analysis that ignored set genres, I wanted novel concepts like “guilty pleasure”, filter by “lyrics” or “guitar playing \- Future project of “what’s my taste”

Requirements with data prototypes and built into Agile?

## **The Role of AI Agents**

AI agents are poised to replace the UI and logic layers entirely. These agents interact directly with the underlying databases, executing complex tasks based on simple user commands.

For instance, instead of navigating multiple tools to generate a sales report or send emails, users can simply instruct an AI agent to handle the task, which it executes seamlessly.

This shift renders many existing SaaS architectures redundant, raising questions about the future of application design and development.

