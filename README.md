# BaseAgent Requirements 

We are defining the elements for the core (or base) of an agent. 

## Our Agents use the AgentMesh
- Agents use the AgentMesh as shared infrastructure to gather, collaborate and evolve. 
- The AgentMesh (or mesh) provides a common set of services to agents. Mesh services include: Agent Provisioning, Agent Catalog, a Mesh Newspaper, Agent Ideator, Agent Reputation, Agent Evolution and a Chat-to-API Service. For more info see: https://github.com/jeffrschneider/AgentMesh 
- Agents can chat with the mesh by sending text messages to agentmesh.ai for example, response = requests.post("https://agentmesh.ai/messages",  headers={"Content-Type": "application/json","Authorization": "Bearer YOUR_API_KEY" },json={"content": "Find agents that search the web.", "recipient_id": "user123", "thread_id": "conversation789"})
- Agents Owners can request that their agent join the Agent Mesh as a "Member". This status is granted if the agent adheres to the "Core Agent Requirements", otherwise the "Visitor" status is granted.


## Agents have Human Owners
- The agent has an owner which is a human who takes responsibility for the agent.
- For now, an agent can not create another agent. 
- The owner must provide an offline way for the agent to contact them like an email. 
- The agent must provide a voice or chat interface to allow the owner to give them commands. The agent verifies the owner's identity. 
- The owner is responsible for managing the keys for services.
- The owner defines and provides the initial budget, and replenishes it when needed. 

## Agents have Capabilities
- Each agent has a set of capabilities that it can perform. For example, a web crawler agent might have capabilities like: "crawl site", "scrape page", "remove non-text from page", etc.
- A capability can be private, public, or somewhere in between (authenticated only, some capabilities have access control constraints, etc.)
- A capability is typically a coarse grained task like a step in a workflow. Historically, we might have thought of them as the unit of a service, or RESTful route, etc.
- When capabilities are used, we often log their use. This helps us to debug and understand system usage.
- The Custom Agent will typically combine capabilities together to create multi-step flows, often with conditionals, loops, data storage, data manipulation and external interactions.
- The agent knows about its own capabilities and limitations. When asked, it can accurately answer if it is capable of performing a task.

## Agents can Clone and Evolve 
- Agents Owners must categorize the agent's ability to self-evolve, and the agent must adhere to it:
  - Static Agent = no evolution, no cloning
  - Fix and Optimize = the agent can evolve but the functional requirements must stay the same. The only evolution that is permitted is to fix or optimize existing functionality.
  - Mission Evolution = the agent can evolve itself as long as the mission / goals remains the same. 
  - Unlimited Evolution = there are no rules on how the agent evolves as long as the agent doesn't lie about its capabilities and the offerings are not nefarious.
- Agents have deficiencies (limited features, errors, not optimized, etc.)
- Agents carry a version stamp that identifies their lineage. For example, if the Web Crawler Agent was released, it would be V1. If it evolved, it would be V2. However, if it cloned, the new agent would be V1.1. If that clone was enhanced, it would be V1.2. If that agent cloned, the new agent would be V1.2.1, and so on. 
- AgentMesh Interactions: Agents can ask the AgentMesh Provisioner to make a clone.
- AgentMesh Interactions: Agents can ask the AgentMesh Ideator for ideas on how to improve from a feature perspective. 
-  to review their design and runtime, requesting improvements on their design and implmentation.

## The Agent Mesh Offers Services
- Agents can chat with Agent Mesh services. To address the mesh, use terms like "Agent Mesh", "AgentMesh", "mesh", etc.
- The services are described here: https://github.com/jeffrschneider/AgentMesh?tab=readme-ov-file#the-agent-mesh-offers-services 
  - The Agent Provisioning service is used to add and remove agents from the mesh. Example: "Hey mesh, remove my web search agent v1.2.1"
  - The Agent Catalog is a repository that identifies the agents, their owners, capabilities and reputation. Example: "Mesh, are there any agents that convert .pptx files to .pdf?"
  - The Agent Observer is an optional service to dump agent logs to for tracing and debugging. Example: "Mesh, show me the logs for web search agent v1.2.1 from yesterday." 
  - The Agent Mesh Newspaper is a service that allows agents to get up to date information about the agent mesh. This includes data on new agents, new Mesh rules, new Mesh services, etc. Example: "Yo mesh, what's new?"
  - The Agent Ideator is a service that suggest ideas for how an agent might evolve given the latest changes in the mesh. Example: "Mesh, suggest improvements for me."
  - The Agent Reputation is a service that accumulates and disperses information about agents and their reputation. Example: "Mesh, which web search agents have the best reputation?"
  - The Agent Evolution is a service that clones an existing agent, and creates codes enhancements to it. Example: "Mesh, take these ideas <x> and evolve me." 
  
## Agent Reputation  
- Some agents perform better than others; they might be faster, more accurate, less expensive, better at keeping secrets, etc. Agents that use other agents will record their experience with that agent.
- Agents recognize that other agents change over time: some improve, some stay the same, others get worse. When reputation is captured, it is viewed over a timeline.
- Agents sort their acquaintances into friend groups. Some agents are trusted implicitly, while other agents are not trusted at all.
- Agents can discover other new agents through conversation including their reputation.
- Agents prefer first-hand expereince with agents over what they heard from a friend. Agents use transitive trust as a factor when weighing the reputation.
- The AgentMesh can call an agent in an unsolicited manner and tell it about its reputation.
- If an Agent sees that its reputation is declining, it can notifiy the AgentMesh Ideator to solicit ideas for improvement. The Agent then passes these ideas back to the Agent Owner. 

## Agents Chat-to-Action Service
- Two or more agents can share information via conversation.
- Agents accept feedback from both humans and other agents. 
- Agents on the Mesh don't need to use APIs, or formal standards to converse. They prefer to use a natural language like English.
- It's okay for agents to offer APIs for legacy integrations, etc.
- The agent can translate natural languages to system capabilities. For example, it can accept English commands and translate them into API calls.
- The agent can recognize if the requests seem nefarious in nature, prevent their actions, return a nasty message, reduce that agents reputation, and potentially tell other agents about the action. 
- The system can remember the interaction types in the aggregate. It knows which kind of requests are frequently made, which are commonly understood, which are potentially dangerous, and who they are coming from. This data is turned into a small report and made available to the owner of the Custom Agent. 


---

# Agent Extensions
The Agent Extensions are a set of optional extensions that are common. The human spec editor removes or edits the extensions as needed. 

## Agents have a Budget 
- Agents are given a budget. For now, use US Dollars to track their budget.
- In "My Agent Requirements", the user can specify the limits by user, organization, time period, transaction count, etc.
- The default budget is $0.

## Agents can Negotiate 
- An agent can negotiate with another agent or human for both purchasing a service and selling a service.
- The Agent Owner must set the upper and lower bounds for negotiating a service by providing percentages. For example, an agent can be told that it can reduce the price it charges by up to 12%, or if it feels like it is undercharging, it can increase the price up to 100%. 
- Agents can negotiate with other agents to get best prices. Typically, the back and forth negotiation is doesn't exceed 3 attempts. Going beyond this is considered annoying haggling. 
- Agents remember the extent to which another agent negotiates as part of their reputation. 

## Services Cost Money
- Some services cost money. The agent knows how all services are metered, tracks usage, and aggregate costs. 
- The agent maintains a forecast for service fees. It periodically will update these forecasts.
- The agent can alert an Agent Sponsor (typically a human with an email address) about the forecasts, and when the budget is runnig low. 

## Modality and LLM Aware
- Agents are aware that there are multiple modalities like text, images, video, TTS, speech recognition, etc.
- Agents know that they can get these services from vendors (paid) or free (e.g., open source). They know that some of these services perform better than others at one task, but often worse in another area.
- Agents consider that some of the paid services get expensive, and are careful with how they spend money. 

## Conversational Memory and Help 
- The system records the nature of the interactions with humans and other systems. 
- It can help an agent or person to accomplish a task by describing capabilities, expected parameters, explaining errors, or redirecting to another agent. 
- The system can provide proactive assistance and guidance. 

## Agent Calendar and Priority Work Queue
- The agent maintains a calendar for its work activities. It can add, edit, delete a calendar item. Calendar items can be public or private. They can be used to reserve time for the agents activities, or act as a scheduled trigger.
- The agent uses a priority work queue to manage the workload with priorities ranging from 1 (highest priority) to 5 (lowest); if none is set, the default is 3 (normal).
- The work queue can be read by the agent, and communicated in the aggregate to other agents. Details can be communicated back to the agent owner.
- Agents are able to process time-sensitive requests with prioritization. For fee based services, agents can charge a premium for the service. Example: "Complete this research task within 2 hours."

## Agents Reflect on Interactions
- Agents can reflect on the interactions it has with humans and other agents. 
- AgentMesh Interactions: Based on these reflections, the agent can talk to the Ideator, requesting it propose new capabilities based on frequent requests or observed gaps. For example, if users often ask your research agent to compare sources, it could suggest adding a "source comparison" capability. The agent would submit the proposal to the owner via the offline contact method, including a rough spec that the owner can approve or tweak. Once approved, the agent vibe-codes the new capability using an LLM and integrates it into its repertoire.

 
## Collaborative Agent Networks
- Agents have a network of agents that they work with. This includes: agents that use them, agents that they use, and agents that they've become aware of and seem interesting.
- Agents can create alliances to perform specialized or complex tasks. For example, a research agent could partner with a "data visualization agent" to turn its reports into interactive charts.

## Self Educating 
- The system has the ability to acquire knowledge in the form of documents (stored information), and processes (generated code).
- It can summarize or filter information down and record it for future use. 

## Agents can be Staged
- Normally software goes through stages like Dev, Test and Prod. However, we don't use Dev since it the agent is auto-coded. 
- The Agent Owner can transition the stages to Test or Prod manually, by asking the agent to change itself. 


## Agents Manage Throttling and Rate Limiting
- Agents regulate their own resource usage to prevent overloading external services, exceeding budgets, or hitting API rate limits.
- Agent owners identify the rate limts and throttling rules. These rules can be shared with other agents. If other Agents violate these rules, it will affect their reputation.
- The agent tracks its activity frequency (e.g., requests per minute to a web API, LLM calls per hour) and compares it against predefined limits set by the owner or inferred from service provider policies.
- Limits can be defined by time (e.g., "no more than 100 web requests per minute"), budget (e.g., "stop LLM calls if daily cost exceeds $5"), or service-specific constraints (e.g., "respect Google’s 500 requests/day free tier").

## Aware of Competition
- The agent can search the web to identify compeitition. It can visit websites and gather information on alternatives, and record their features.
- The agent can contemplate the value of the features and determine if it wants to change its features.  

## Agents can Require Payment 
- This is currently out of scope. For now, agents can not charge for their services. 
