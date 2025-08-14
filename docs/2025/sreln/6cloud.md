# 6 Cloud Native Test

**1. Which of the following is a recommended practice for defining interfaces in API development? **

- 1 Defining interfaces after development is complete 
- 2 Defining interfaces only for current use cases 
- 3 Defining interfaces to support current, planned, and foreseeable uses 
- 4 Defining interfaces without considering reuse

The correct answer is:

**3 — Defining interfaces to support current, planned, and foreseeable uses** ✅

In API development, you want your interface design to be **forward-looking**. That means:

* Cover **current requirements**
* Anticipate **planned features**
* Allow for **reasonable future expansion** without breaking backward compatibility

This approach reduces costly redesigns and avoids breaking changes for API consumers later.

Defining interfaces *only* for the current state (option 2) or after development is done (option 1) is risky, and skipping reuse considerations (option 4) is a common cause of technical debt.

**2 Which of the following is a recommended practice for registering interfaces in API development**

- 1 Registering only interfaces that are currently exposed as endpoints
- 2 Not registering any interfaces that are not currently exposed as endpoints
-** 3 Registering all interfaces that are currently exposed as endpoints**
- 4 Registering all interfaces even if not currently exposed as endpoints

The correct answer is:

**3 — Registering all interfaces that are currently exposed as endpoints** ✅

In API development, **registration** is about making exposed endpoints discoverable and documented—usually in an API registry, developer portal, or service catalog.

* **Why not 1 or 2?** Those could mean leaving some exposed endpoints unregistered, which is risky because undocumented APIs are harder to maintain and more prone to misuse.
* **Why not 4?** You shouldn’t register endpoints that are not actually exposed—this could cause confusion or security risks.

So the best practice is:
🔹 **Register every API interface you expose** so it’s documented, searchable, and properly governed, but don’t register internal-only or unused interfaces.


**4 Which of the following is a recommended practice for using common context and library services in API development?**

- 1 Ignoring common context and library services
- 2 Using only some common context and library services
- **3 Using all common context and library services**
- 4 Using common context and library services only for security

The correct answer is:

**3 — Using all common context and library services** ✅

In API development, **common context and library services** (e.g., logging, security, error handling, metrics, authentication, configuration management) are designed to ensure **consistency, reusability, and maintainability** across APIs.

* Using **all** of them ensures every API benefits from the shared standards and avoids duplication of effort.
* Option 1 ignores them entirely — leading to inconsistent implementations.
* Option 2 cherry-picks, which can cause gaps in functionality or governance.
* Option 4 limits them to security, missing other key benefits like observability, performance, and maintainability.


**5 Are applications built according to a data-centric design?**

- 1 Applications are built according to an application-centric design, data is data-centric
- 2 Applications are built according to a user-centric design, data is data-centric
- 3 Applications are built according to a data-centric design
- 4 It depends on the type of application

The correct answer is:

**1 — Applications are built according to an application-centric design, data is data-centric** ✅

In architecture principles:

* **Applications** are generally designed around **specific business functions and use cases** → application-centric.
* **Data** is designed, modeled, and governed in a **data-centric** way → independent of any single application so it can be reused and shared.

This distinction is important because while applications may change or be replaced, **data persists** and needs a consistent, centralized design.


**6 what is considered the central assest of the enterprise **

- 1 employees 
- 2 technology 
- **3 data** 
- 4 customers

The correct answer is:

**3 — Data** ✅

In modern enterprise architecture, **data** is considered the **central asset** because:

* Applications, technology, and even business processes can change, but **data has long-term value**.
* Data drives decision-making, analytics, compliance, and innovation.
* Protecting and managing data well directly impacts the organization's competitiveness and resilience.

Employees, technology, and customers are all critical, but **data is the persistent, reusable core asset** across all business operations.


**7 can unstructured data be as valuable as structured data**

- 1 no
- **2 yes, but only in certain cases**
- 3 yes always
- 4 it depends on the type of unstructured data

The correct answer is:

**2 — Yes, but only in certain cases** ✅

Unstructured data (like emails, documents, images, videos, social media posts, sensor readings) **can be highly valuable**—for example, in sentiment analysis, fraud detection, or medical imaging.

However:

* Its value depends on **context, quality, and the ability to process and interpret it**.
* Without the right tools and analysis, unstructured data might be hard to exploit effectively.

So, while it *can* match or exceed the value of structured data, it’s not *always* the case.


**8 what does a data-centric enterprise have besides a tech implementation**

- 1 an associated culture and mindset
- 2 no associated culture or mindset
- 3 only a tech implementation
- 4 only an associated culture and mindset

The correct answer is:

**1 — An associated culture and mindset** ✅

A **data-centric enterprise** isn’t just about having the right technology stack; it also requires:

* A **culture** that treats data as a shared, strategic asset.
* A **mindset** where teams prioritize data quality, governance, and reuse.
* Policies and processes that ensure data is consistent, accessible, and trusted.

Without that cultural foundation, even the best technology won’t deliver the full benefits of being data-centric.

**9 how are data products described?**

- 1 based on a common taxonomy and ontology
- 2 based on individual preferences
- 3 based on the type of data product
- 4 based on stage of creation and evolution

The correct answer is:

**1 — Based on a common taxonomy and ontology** ✅

Data products should be described **consistently** so they can be easily understood, discovered, and integrated across the enterprise.

* **Common taxonomy** → shared classification system for organizing data products.
* **Common ontology** → shared definitions of terms, relationships, and meaning, ensuring everyone interprets data the same way.

Using individual preferences (option 2) or inconsistent criteria (options 3 or 4) would lead to confusion and make data harder to reuse.

**10 what is expected of stakeholders in a system**

- 1 they should not make data-driven decisions
- 2 they should make data-driven decisions during development
- 3 they should make data-driven decisions only  during production
- 4 they will make data-driven decisions to validate ideas and reduce uncertainty. These may be automated

The correct answer is:

**4 — They will make data-driven decisions to validate ideas and reduce uncertainty. These may be automated** ✅

In a well-designed system:

* Stakeholders rely on **data** to guide choices, not just intuition.
* Decisions are made **throughout the lifecycle** — from early validation of ideas to ongoing optimization.
* Automation can speed up decision-making and reduce human bias, especially for repetitive or real-time scenarios.

This approach ensures that decisions are **evidence-based** and uncertainty is minimized.


**11 what is required for the information supplied to improve observability**

- 1 it must be unintelligible and unrelated
- 2 it must be intelligible and unrelated
- 3 it must be unintelligible and relatable
- **4 it must be intelligible and relatable**

The correct answer is:

**4 — It must be intelligible and relatable** ✅

For observability, the information you collect should:

* **Be intelligible** → clear, understandable, and meaningful to the people or systems consuming it.
* **Be relatable** → connected to the system’s context, so it helps explain behaviors, issues, and performance.

If data is unclear or irrelevant, it won’t help identify problems or improve system understanding.

**12 what is expected of an enterprise regarding control plane information collection**

- 1 collect as little information as possible
- 2 collect information only for specific use cases
- 3 collect as much info as possible and use it to dynamically improve resilience, reliability, and performance
- 4 collect info only for expected future use cases


The correct answer is:

**3 — Collect as much info as possible and use it to dynamically improve resilience, reliability, and performance** ✅

In an enterprise:

* The **control plane** monitors and manages systems, applications, and infrastructure.
* Collecting **comprehensive information** allows the enterprise to **detect issues early, optimize performance, and adapt dynamically**.
* Limiting data collection (options 1, 2, or 4) risks blind spots and reduces the system’s ability to self-heal or improve.


**13 what is required for backing service**

- 1 they cannot be exchanged during other operation
- 2 they can only be exchanged during deployment
- **3 they can be exchanged during operation without redeploying the application**
- 4 they cannot be exchanged at all

The correct answer is:

**3 — They can be exchanged during operation without redeploying the application** ✅

A **backing service** (like databases, message queues, or caches) should be:

* **Replaceable at runtime** without requiring changes or redeployment of the application.
* This allows for **flexibility, scaling, and resilience**, letting the system swap services if needed (e.g., for upgrades, failover, or switching vendors).

Options 1, 2, and 4 would limit operational flexibility and violate the principles of modern, cloud-native design.


**14 what is the requirement for services specific to an application?**

- **1 they should be deployed and enabled as part of deploying an application**
- 2 they should not be part of deploying an application
- 3 they should be specific to each environment
- 4 they should not be enabled during deployment

The correct answer is:

**1 — They should be deployed and enabled as part of deploying an application** ✅

Services that are **specific to an application** (like certain APIs, caches, or supporting microservices) must:

* Be **deployed together with the application** to ensure it functions correctly.
* Be **enabled automatically** during deployment to avoid manual configuration errors.

Options 2, 3, and 4 would risk the application failing to run properly or inconsistencies across environments.

**15 which of following is true about a codebase**

- 1 it can be forked for different deployment
- 2 different deployment may have different codebase
- **3 it must be singular even it different have configuration that apply to them**
- 4 env specific code be included in the codebase


**3 — It must be singular even if different deployments have configurations that apply to them** ✅

In modern development practices:

* A **single codebase** is maintained for all deployments to ensure consistency, easier maintenance, and streamlined updates.
* **Deployment-specific variations** (like environment settings, feature flags, or secrets) are handled through **configuration**, not by duplicating or forking the code.

Options 1, 2, and 4 go against best practices because forking or including environment-specific code leads to **complexity, drift, and maintenance challenges**.


**16 what is the importance of maintaining links in a codebase**

- 1 it allows for forking of the codebase
- 2 it ensures that different deployment have different codebases
- **3 it defines the codebase and ensures it remains singular**
- 4 it allows for env specific code to be included in the codebase

The correct answer is:

**3 — It defines the codebase and ensures it remains singular** ✅

Maintaining links (like references to modules, libraries, or dependencies) is important because:

* It **keeps the codebase coherent and unified**.
* Ensures all deployments use the **same underlying code** while allowing configuration to handle environment-specific differences.
* Prevents fragmentation or duplication that can lead to maintenance challenges and inconsistencies.

Options 1, 2, and 4 would undermine the principle of a single, maintainable codebase.


**17 what does the enterprise do with collected information**

- 1 it use to maintain it's current security posture
- **2 it use to improve its security  posture dynamically**
- 3 it uses it to improve  its security  posture statically


The correct answer is:

**2 — It uses it to improve its security posture dynamically** ✅

In a modern enterprise:

* Collected information (logs, metrics, alerts, access patterns) is **continuously analyzed**.
* This allows the enterprise to **adapt and enhance security in real time**, responding to threats, vulnerabilities, or anomalous behavior.
* Static approaches (option 3) or using data only for maintenance (option 1) are insufficient in dynamic, evolving environments.


18 how is access to individual enterprise resources granted

- **1 on a atomic basis**
- 2 on a large basis
- 3 on a case-by-case basis
- 4 on a device-by-device basis

The correct answer is:

**1 — On an atomic basis** ✅

In a data-centric enterprise, **access control is fine-grained**:

* Permissions are granted **at the smallest logical unit of a resource** (atomic level), such as a specific record, file, or service operation.
* This ensures **least-privilege access**, improving security and reducing the risk of unauthorized use.

Options 2, 3, and 4 are less precise and do not provide the same level of control and protection.

19 what is an attribute of an independent service

- 1 being stateful
- 2 being able to share state with other services
- 3 being able to predict its halting externally
- **4 being stateless**

The correct answer is:

**4 — Being stateless** ✅

An **independent service** in modern architectures (like microservices) is typically:

* **Stateless**, meaning it does not retain client-specific data between requests.
* This enables **scalability, resilience, and easier deployment**, because any instance of the service can handle any request.

Options 1 and 2 (stateful or sharing state) reduce independence, and option 3 (predicting halting externally) is not a defining attribute of independence.

**20 what is required for publishing events**

- 1 must have a defined schema and be published using registered topic
- 2 must have a defined schema and published using unregistered topic
- 3 they do not have defined schema
- 4 they can be published without using topic

The correct answer is:

**1 — Must have a defined schema and be published using a registered topic** ✅

For event-driven systems:

* A **defined schema** ensures that consumers understand the structure and meaning of the event data.
* Using a **registered topic** ensures discoverability, governance, and proper routing of events across the system.

Options 2, 3, and 4 would lead to **ambiguity, integration issues, and potential system errors**.


**21 what must be clear when cloud services scale with demand**

- 1 the cost of scaling 
- 2 the root cause of the demand
- 3 the number of users
- 4 the type of service

The correct answer is:

**1 — The cost of scaling** ✅

When cloud services scale dynamically with demand, it’s essential to understand **how much it will cost** to handle increased load.

* This allows the organization to **budget appropriately** and avoid unexpected expenses.
* While understanding the number of users, root cause of demand, and type of service can be useful, the **primary concern for scaling decisions** is the financial impact.


**22 how are cloud services charged**

- 1 to the users of the service
- 2 to the developer of service
- **3 to the owner of service**
- 4 to the admin  of service

The correct answer is:

**3 — To the owner of the service** ✅

In cloud computing:

* **The service owner** is responsible for costs associated with running the service, including compute, storage, networking, and any managed services used.
* Users of the service typically **consume** the service but do not directly pay the underlying cloud provider unless a billing model is set up.
* Developers or admins may influence costs but are not typically charged directly.
