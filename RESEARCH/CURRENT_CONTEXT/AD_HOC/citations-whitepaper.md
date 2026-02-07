# Whitepaper Citations & Research Sources

> Extracted quotes and references from AD_HOC research materials, organized by theme for whitepaper integration.

---

## Theme 1: AI Changes the PM/Engineering Dynamic

### Andrew Ng (Y Combinator, 2025)
**Source:** [YouTube](https://youtu.be/RNJCfif1dPY)

> "Product management work—getting user feedback, deciding what features to build—is increasingly the bottleneck... I'm seeing very interesting dynamics in multiple teams over the last year... the engineers have gotten so much faster."

> "For the first time in my life... a team proposed to me having twice as many PMs as engineers... I still don't know if this proposal is a good idea but I think it's a sign of where the world is going."

> "PMs that can code or engineers with some product instincts often end up doing better."

**On concrete vs vague requirements:**
> "If you tell me 'use AI to optimize healthcare assets'... different engineers would do totally different things and because it's not concrete you can't build it quickly... In contrast, 'let patients book MR machine slots online'—that is concrete and engineers can build it quickly."

**On everyone coding:**
> "I think it's time for everyone of every job role to learn to code... my CFO, my head of talent, my recruiters, my front desk person—all of them know how to code and I see all of them performing better at all of their job functions."

---

## Theme 2: Culture Before AI

### HBR / Deloitte - Nitin Mittal
**Source:** HBR IdeaCast - "How Generative AI Changes Organizational Culture"

> "No AI system is going to magically somehow be responsible by itself without the culture of that organization being responsible onto itself to start with. It cannot be dictated by the CEO, it cannot be governed by the board, it cannot be mandated by the leadership."

**Organizations that miss the mark:**
> "The organizations that absolutely miss the mark have got this viewpoint that, 'Well, this is yet another technology, probably going through a hype cycle, and consequently we'll just have this particular group in IT, or this data science function, just look into it and take it forward.'"

**Organizations that succeed:**
> "Those organizations who actually view this as a moment in time where they have to question the basis of how do they compete, how do they thrive, what changes do they need to make, both from a product or a service perspective, but more important from a culture and a people standpoint, actually are the ones who are able to progress forward."

### HBR / Harvard Business School - Tsedal Neeley
**Source:** HBR IdeaCast - "How Generative AI Changes Organizational Culture"

> "Currently, organizations are neither set up for it, nor do they fully understand it, but the adoption and the curiosity around it has been extraordinary."

> "The first thing that organizations need to ensure happens is that people understand these technologies fully. To really develop some form of fluency... what the technology is, what it isn't, what are the limitations, what are the risks, and what are the opportunities."

**Hearts and Minds framework:**
> "Imagine a two by two... On one side, you have buy-in, where people have to believe that this is important. And another dimension is the belief that they could do it—do I have the self-efficacy for this?"

**Bottom-up pressure:**
> "What is being noticed and observed in many of these organizations is that the pressure to move ahead at speed and with skill is coming from the employees themselves. If we don't provide them these particular tools... they are going to find their own ways."

---

## Theme 3: AI Architecture & Agent Design

### LLM Agent Best Practices (Technical Report)
**Source:** AD_HOC/LLM Agent Best Practices.md

**Agentic vs static RAG:**
> "The goal shifts from simply 'finding the best documents' to 'conducting the best research process to answer the user's question.'"

**Reflection is critical:**
> "A simple 'plan-then-act' agent without a feedback mechanism is essentially a deterministic workflow that is prone to failure when it encounters unexpected results."

**Agentic paradigm shift:**
> "The value proposition of an agentic research tool is not solely the quality of the final report it generates, but the autonomy, efficiency, and reliability of the research *process* it designs and executes."

**Reasoning framework comparison:**

| Framework | Best For | Key Limitation |
|-----------|----------|----------------|
| Chain-of-Thought (CoT) | Linear reasoning, math | No backtracking |
| Tree-of-Thought (ToT) | Exploration, puzzles | Computationally expensive |
| ReAct | Research, fact-finding | Dependent on tool quality |

---

## Theme 4: This Moment Is Different

### Eric Evans (DDD Europe)
**Source:** AD_HOC/Eric Evans DDD Europe.md

> "There have been many attempts at AI over the years that produced various things of varying levels of usefulness but this one seems different to me. When I think about this one it feels like the late 90s and the web arriving."

> "It was at first maybe not such a big thing... it seemed like enterprise software would probably go on its own path... and yet here we are—the web is everything. And it feels that way to me this time again."

> "We don't have to know what's going to happen in order to get ready."

---

## Theme 5: Applications Are Thin Layers Over Data

### Satya Nadella / David Chan Analysis
**Source:** AD_HOC/Blog_ Enterprise is Dead - Satya.md

**Applications as UI over databases:**
> "Traditional business applications are essentially thin user interface (UI) layers over databases, designed to perform basic operations—create, read, update, and delete (CRUD)... in the age of AI, the need for such intermediary layers is rapidly diminishing."

**AI agents replace UI layers:**
> "AI agents are poised to replace the UI and logic layers entirely. These agents interact directly with the underlying databases, executing complex tasks based on simple user commands."

**Data vs Code insight:**
> "Data you only have 'what's there' where in theory, code can create many many many perturbations of logic + interface with the same set of data—so when you work backwards, you are constrained by the data, but relatively unconstrained by the code."

---

## Cross-Reference to SemOps Themes

| Citation Theme | SemOps Connection |
|----------------|-------------------|
| PM as bottleneck (Ng) | Product Management perspective; concrete requirements = semantic precision |
| Culture before AI (Mittal) | Human factor; commitment problem; organization-wide coherence |
| Hearts and Minds (Neeley) | Buy-in + self-efficacy = the human factor |
| Reflection in agents | Semantic flywheel; continuous optimization |
| "This feels different" (Evans) | Why rethink now; inflection point |
| Apps as thin layers (Nadella) | Data is the real asset; schema matters more than UI |

---

## Usage Notes

- These quotes can support whitepaper arguments
- Cite original sources (YouTube links, HBR, etc.) not this compilation
- Eric Evans quote provides DDD legitimacy for the approach
- Andrew Ng quote supports PM-centric framing
- Tsedal Neeley's Hearts & Minds maps to Human Factor section
