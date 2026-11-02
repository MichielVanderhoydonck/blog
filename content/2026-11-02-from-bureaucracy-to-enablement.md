+++
title = "From Bureaucracy to Enablement: What DevOps Can Teach Us About Tackling Global Challenges"
slug = "from-bureaucracy-to-enablement"
date = "2026-11-02"
description = "Why solving complex, interconnected problems requires better coordination, shared infrastructure and less organisational friction."
[taxonomies]
tags = ["DevOps", "Governance", "Platform Engineering", "Culture"]
+++

# Bigger than the individual

Like many people, I was taught about climate change in school, the challenges it brings and the need to act. Over the past decades, we've seen technological advancements as well as government initiatives aimed at addressing it. Yet we're still falling short of what's needed.

I don't intend to evangelise particular ideas or give you another list of things you can do as an individual. Instead, I'm more curious about what makes progress so difficult and what keeps us from achieving broader goals, rather than simply kicking the proverbial can down the road.

We aren't exactly starting from scratch. We have renewable energy, nuclear power, energy storage and increasingly efficient technologies. We also have decades of research, international agreements and growing awareness of the challenges ahead. Yet having solutions available doesn't automatically translate into implementing them at the scale and pace required.

One reason is that these challenges rarely exist in isolation. Energy, industry, infrastructure, economics and politics are deeply interconnected. Decisions made by individual countries or organisations can have consequences far beyond their own boundaries, while their incentives and priorities don't always align with the broader goals we're trying to achieve.

This is something I recognise from my own field of software engineering. Technology is often readily available and many technical problems have solutions. Yet friction remains in how we orchestrate and coordinate the work that needs to be done. In this post, I want to explore whether some of the lessons we've learned about building and operating complex software systems could offer a useful perspective on the challenges of climate governance.

# The coordination problem

In software engineering, individual tasks are often agreed upon and owned by a team. But when a company spans multiple teams and follows an ever-evolving strategy, making sure those teams work towards compatible goals becomes a fundamental requirement.

Individual team members solving problems independently in undocumented ways, or teams tackling issues only to throw them over the wall for others to consume, are familiar anti-patterns in our industry. We've developed many processes and practices to address these problems, and for good reason. People are generally more motivated when they feel heard, improvements gain traction beyond their immediate team, and the benefits extend throughout the organisation and ultimately to its customers.

Consider a team optimising its own service without accounting for the teams consuming it. Or a development team delivering software without considering how it will be operated. These decisions might make sense locally but create friction and additional work elsewhere.

Now apply the same thinking to energy. A country investing heavily in renewable generation might still struggle to make the most of that capacity without sufficient grid infrastructure, storage or connections to neighbouring countries. The investment itself isn't necessarily wrong, but its effectiveness depends on decisions and infrastructure beyond its borders.

Making a simple comparison between individuals and organisations, teams and countries, and their respective responsibilities reveals some interesting similarities. As an industry, we've come a long way in recognising these challenges. Engineering cultures and practices such as DevOps have emerged to address exactly these kinds of friction points through shared ownership, collaboration and automation. Many organisations are still working through that adoption, but the benefits are increasingly tangible.

# From control to enablement

From a strategic perspective, governance is important. Objectives need to be established, boundaries defined and responsibilities made clear. Quite often, however, the mechanisms introduced to achieve this end up creating more hurdles than necessary, slowing down innovation and progress.

We've seen this play out repeatedly in software engineering. In my experience, capturing principles, establishing guidelines and providing sensible defaults are important, but enforcing every detail through rigid processes and bureaucracy rarely scales well. Teams need the confidence and autonomy to make decisions, experiment and deliver value. At the same time, organisations need to avoid creating an environment where teams resort to shadow IT, undocumented workarounds or entirely separate solutions simply to get things done.

This is where governance and bureaucracy start to diverge. Good governance provides direction and boundaries. Excessive bureaucracy makes navigating those boundaries an objective in itself.

Living in Belgium, with its many layers of government and often fragmented decision-making, makes this particularly recognisable. The same challenge exists at a European level. We have ambitions that require collaboration across borders, yet implementation often remains fragmented across national and regional institutions. Rather than always enabling countries to work towards shared objectives, additional layers of regulation and coordination can sometimes make progress more cumbersome. I recently attended a book presentation by [David Criekemans](https://www.uantwerpen.be/en/staff/david-criekemans/) on *Hyperrealism, Europe in a new geopolitical era*, which touched upon exactly these kinds of geopolitical hurdles.

Despite the wars, economic pressures and geopolitical uncertainty we currently face, there are also reasons for optimism. New economic and strategic partnerships are emerging, creating opportunities for collaboration, innovation and better coordination. Energy is a particularly interesting example: building a more interconnected European energy system requires countries to look beyond their own borders and recognise the benefits of shared infrastructure and common objectives.

Perhaps the direction we need to move towards is not more centralised control, but better enablement. Establishing the right boundaries and shared capabilities, while allowing those operating within them to make meaningful decisions.

# Platform engineering as an enabler

So how do we strike a balance between having enough governance to keep things aligned, while still allowing teams to move independently and make progress? In software engineering, one approach that has gained traction over the years is platform engineering.

Rather than having a central team dictate every implementation detail or act as a gatekeeper for every change, the idea is to provide shared capabilities that teams can build upon. Think of reusable CI/CD pipelines, infrastructure provisioning, observability tooling, security controls and well-documented interfaces. These provide a common foundation without requiring every team to reinvent the wheel.

The distinction might seem subtle, but it fundamentally changes how teams work together. Instead of requesting infrastructure through tickets and waiting for someone else to provision it, teams can consume it through self-service workflows. Instead of relying on manual security reviews for every deployment, common policies can be built into the platform and enforced automatically. And instead of mandating one rigid implementation, teams can follow established golden paths while retaining the ability to deviate when their circumstances require it.

This doesn't mean removing ownership or accountability. Quite the opposite. Teams remain responsible for what they build and operate, while the platform provides the capabilities and guardrails to help them do so effectively. The platform team itself needs to understand its users, gather feedback and continuously improve what it provides.

There are some interesting parallels here with the challenges we've discussed around energy and European cooperation. Shared grid infrastructure, common technical standards and cross-border interconnectors are not unlike the shared capabilities we build in engineering organisations. They allow individual participants to operate independently while benefiting from a larger, interconnected system.

A country doesn't necessarily need to manage its entire energy system around the needs of every other country. But it does benefit from compatible standards, reliable connections and mechanisms to exchange energy when needed. Similarly, a software team doesn't need to share every implementation detail with its neighbours, but it does benefit from well-defined interfaces and shared foundations.

The goal isn't to centralise every decision. It's to make collaboration easier and reduce duplicated effort, while allowing individual participants to contribute to a wider system.

## Beyond software: enabling cooperation across borders

We can see similar approaches emerging beyond software engineering. Take the growing cooperation between Europe and North Africa around renewable energy. Morocco has significant potential for solar and wind generation, and there is increasing interest in connecting that capacity to European energy markets. France and Morocco, for example, announced [plans](https://www.reuters.com/business/energy/france-morocco-plan-direct-electricity-link-2026-07-16) in July 2026 to explore a direct electricity interconnection that could facilitate exports of Moroccan renewable energy to France. Making this work requires more than generation capacity: it also depends on transmission infrastructure, compatible market rules, regulatory cooperation and long-term investment.

The same principle extends beyond geographical neighbours. In September 2026, Belgium and India renewed [their cooperation](https://inde.diplomatie.belgium.be/en/india-belgium-joint-statement-visit-prime-minister-belgium) on renewable energy, covering areas such as solar power, offshore wind, energy storage, green hydrogen and pumped hydro. More recently, the European Union and Canada announced their [Alliance for the Future](https://www.consilium.europa.eu/en/meetings/international-summit/2026/10/29-30), which includes closer cooperation on energy security, sustainable grids, battery storage and critical raw materials.

These partnerships don't require all participants to follow identical strategies or surrender their autonomy. They create common ground and opportunities for investment and knowledge exchange. Whether those opportunities translate into tangible results depends on how effectively the agreements are implemented.

In many ways, this reflects what a good engineering platform aims to achieve: providing the foundations, standards and interfaces that allow independent participants to accomplish more together.

# What could this look like at scale?

Rather than asking every country to independently solve its energy challenges, what if we approached the transition as a shared infrastructure problem? Different regions have different strengths, resources and constraints. Connecting them effectively could produce a system that is more resilient, efficient and sustainable than the sum of its individual parts.

Consider the opportunities across Europe and its neighbouring regions:
- **Complementary generation:** The North Sea offers considerable potential for offshore wind, the Mediterranean and North Africa have abundant solar resources, Nordic countries contribute hydroelectric capacity and storage, while nuclear power provides dependable low-carbon baseload where it makes sense.
- **Interconnected transmission:** Cross-border interconnectors, high-voltage direct current (HVDC) lines and coordinated grid planning help balance supply and demand across regions, much like distributed systems rely on reliable communication between components.
- **Shared flexibility:** Energy storage, demand response and flexible industrial consumption help accommodate the variable nature of renewable generation across borders, rather than expecting every region to maintain redundant capacity for every possible scenario.
- **Beyond electricity:** In regions with abundant generation and limited freshwater resources, clean power can support desalination, green hydrogen production where direct electrification isn't practical, and local industrial development.

North Africa is particularly interesting in this regard. Renewable energy investments could serve local communities and industries while also creating opportunities for energy exports to Europe. That requires more than solar panels and transmission cables, of course: water management, environmental safeguards, local ownership and fair economic partnerships are equally important.

The broader point is that we should look beyond individual generation projects or national energy portfolios. In software engineering, we recognise this as designing for the system as a whole rather than optimising individual components. By coordinating infrastructure, investment and resources, we can create opportunities that would be difficult to realise independently.

# Five principles for better coordination

Looking back at the examples across both domains, there are a few principles that stand out. They're not particularly revolutionary, and most of them are already well established in software engineering. Yet applying them consistently can make a substantial difference.

### 1. Optimise for outcomes, not activity
Having processes, initiatives and projects in place doesn't necessarily mean we're making progress. In software engineering, we increasingly look at outcomes such as reliability, delivery performance and customer value rather than simply counting deployments or completed tickets. The same thinking applies to the energy transition: installed renewable capacity matters, but so do actual emissions reductions, energy security and grid reliability.

### 2. Make the preferred path the easiest path
Good platforms provide sensible defaults and accessible capabilities that make the desired approach the natural choice. If doing things the right way requires navigating endless approval processes, people will eventually look for alternatives. Whether we're provisioning infrastructure or developing cross-border energy projects, reducing unnecessary friction helps turn ambition into execution.

### 3. Make boundaries predictable
In engineering, automated policy enforcement, security checks and deployment controls allow teams to operate independently within established boundaries. Similarly, predictable regulatory frameworks and transparent standards can reduce uncertainty and make cooperation between countries and industries easier.

### 4. Establish clear ownership
Shared responsibility doesn't mean that nobody is accountable. In software organisations, teams need to understand what they own, what they depend on and where their responsibilities end. The same applies to shared infrastructure: collaboration requires clear agreements on investment, maintenance, operation and accountability.

### 5. Build feedback loops
No platform, process or strategy is perfect from the outset. Observability, service-level objectives and continuous improvement help engineering organisations identify problems and adapt. Energy systems and climate policies need similar feedback mechanisms, using transparent data and measurable outcomes to understand what works and where adjustments are needed.

Ultimately, these principles are about creating an environment where independent participants can make progress without constantly working around the system they're part of.

# Where the analogy breaks down

Of course, there are limits to this comparison. Governments aren't software companies, citizens aren't workloads and public infrastructure isn't a commercial product.

Democratic accountability, fundamental rights and the distribution of costs and benefits cannot simply be translated into engineering metrics. Countries have legitimate differences in priorities, resources and political mandates. Infrastructure also comes with geographical, environmental and economic constraints that cannot be abstracted away.

There is a danger in applying engineering thinking too literally: optimising a system doesn't automatically mean making the right societal choices.

The value of the comparison isn't in suggesting that governments should operate like platform teams. It's in recognising that some of the challenges we encounter in engineering organisations, such as fragmented ownership, poor interfaces, misaligned incentives and excessive coordination overhead, also appear in much larger systems.

We can borrow useful principles without pretending the underlying problems are identical.

# Better orchestration, not more control

Looking back at the initial question, climate change is an enormous coordination challenge. We have many of the technologies and much of the knowledge needed to make substantial progress, but deploying them at scale requires more than individual actors making better decisions—it requires institutions that can coordinate investment, infrastructure and incentives across boundaries.

The same lesson applies to software organisations. When complexity increases, accumulating more processes and centralising more decisions is often the easiest response. But the better long-term answer is to improve the system itself: its interfaces, shared capabilities, feedback loops and incentives.

Perhaps we don't need everyone to think alike, operate identically or surrender their autonomy. We need shared objectives, compatible interfaces, clear responsibilities and systems that make collaboration worthwhile.

In the end, effective coordination isn't about controlling every participant. It's about creating the conditions in which they can achieve more together than they ever could independently.