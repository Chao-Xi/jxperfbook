
# 4 SRE Test


**1 Which of the following is a recommended practice for defining interfaces in API development?**


- 1 Defining interfaces after development is complete
- 2 Defining interfaces only for current use cases
- 3 Defining interfaces to support current, planned, and foreseeable uses
- 4 Defining interfaces without considering reuse

The recommended practice is:

**✅ 3 — Defining interfaces to support current, planned, and foreseeable uses**

In API development, designing interfaces with **future scalability and flexibility** in mind helps avoid breaking changes later, supports reuse, and ensures that the API remains relevant as requirements evolve.

**2 Which of the following is a recommended practice for registering interfaces in API development**

- 1 Registering only interfaces that are currently exposed as endpoints
- 2 Not registering any interfaces that are not currently exposed as endpoints
- 3 Registering all interfaces that are currently exposed as endpoints
- 4 Registering all interfaces even if not currently exposed as endpoints

The recommended practice is:

**✅ 3 — Registering all interfaces that are currently exposed as endpoints**

This ensures that:

* API consumers have a clear, up-to-date catalog of what’s actually available.
* Documentation and discovery tools (like API gateways or developer portals) are accurate.
* You avoid cluttering the registry with unused or internal-only interfaces.

**3 Which of the following is a recommended practice for using common context and library services in API development?**

- 1 Ignoring common context and library services
- 2 Using only some common context and library services
- 3 Using all common context and library services
- 4 Using common context and library services only for security

The recommended practice is:

**✅ 3 — Using all common context and library services**

This promotes **consistency**, **maintainability**, and **security** across APIs by ensuring all services (e.g., logging, authentication, error handling, configuration management) follow shared standards. It also reduces duplication and simplifies long-term maintenance.

**4 Are applications built according to a data-centric design?**

- 1 Applications are built according to an application-centric design, data is data-centric
- 2 Applications are built according to a user-centric design, data is data-centric
- 3 Applications are built according to a data-centric design
- 4 It depends on the type of application

The correct answer is:

**✅ 1 — Applications are built according to an application-centric design, data is data-centric**

In most architectures:

* **Applications** are designed around functionality, workflows, and business logic — hence *application-centric*.
* **Data** is designed, modeled, and managed according to data-centric principles to ensure integrity, consistency, and reuse across systems.

**5 what is considered the central asset of the enterprise**

- 1 employees
- 2 technology
- 3 data
- 4 customers

The correct answer is:

**✅ 3 — Data**

In modern enterprise architecture, **data** is considered the central asset because it:

* Drives decision-making and innovation.
* Retains value even as technology and applications change.
* Can be reused across multiple applications and business processes.

Employees and customers are essential, but in the **information management** context, data is the primary enduring asset.


**4 can unstructured data be as valuable as structured data**

- 1 no
- 2 yes, but only in certain cases
- 3 yes always
- 4 it depends on the type of unstructured data

The correct answer is:

**✅ 3 — yes always**

Unstructured data — like emails, documents, videos, social media posts — can be just as valuable as structured data because it often contains **rich context, insights, and patterns** that structured data alone can’t capture.

Its value depends more on how effectively it’s managed, processed, and analyzed, not on its format.

**5 What does a data-centric enterprise have besides a tech implementation**

- 1 an associated culture and mindset
- 2 no associated culture or mindset
- 3 only a tech implementation
- 4 only an associated culture and mindset

The correct answer is:

**✅ 1 — an associated culture and mindset**

A truly **data-centric enterprise** isn’t just about having the right technology — it also requires:

* A **culture** that treats data as a core asset.
* A **mindset** where decisions are driven by data and data stewardship is everyone’s responsibility.
* Processes and governance that reinforce data quality, accessibility, and reuse.


**6 how are data products described?**

- 1 based on a common taxonomy and ontology
- 2 based on individual preferences
- 3 based on the type of data product
- 4 based on stage of creation and evolution

The correct answer is:

**✅ 1 — based on a common taxonomy and ontology**

Data products are best described and classified using a **common taxonomy and ontology** to ensure consistent understanding, discoverability, and interoperability across the enterprise. This helps align teams and systems around shared definitions and relationships.


**7 what is expected of stakeholders in a system**

- 1 they should not make data-driven decisions
- 2 they should make data-driven decisions during development
- 3 they should make data-driven decisions only  during production
- 4 they will make data-driven decisions to validate ideas and reduce uncertainty. These may be automated

The correct answer is:

**✅ 4 — they will make data-driven decisions to validate ideas and reduce uncertainty. These may be automated**

Stakeholders are expected to leverage data continuously—both during development and production—to guide decisions, improve outcomes, and reduce risks. Automation can further enhance this process by making timely, data-driven actions.


**8 what is required for the information supplied to improve observability**

- 1 it must be unintelligible and unrelated
- 2 it must be intelligible and unrelated
- 3 it must be unintelligible and relatable
- 4 it must be intelligible and relatable

The correct answer is:

**✅ 4 — it must be intelligible and relatable**

For information to improve observability, it needs to be **clear (intelligible)** so that it can be understood easily, and **relatable** so it connects meaningfully to the system’s components or behaviors being observed. This enables effective monitoring, diagnosis, and decision-making.


**9 what is required for backing service**

- 1 they cannot be exchanged during othe peration
- 2 they can only be exchanged during deployment
- 3 they can be exchanged during operation without redeploying the application
- 4 they cannot be exchanged at all

The correct answer is:

**✅ 3 — Collect as much info as possible and use it to dynamically improve resilience, reliability, and performance**

Enterprises are expected to gather comprehensive control plane information so they can continuously monitor and adapt systems to enhance stability, reliability, and efficiency dynamically.


**10 what is the requirement for services specific to an application?**

- 1 they should be deployed and enabled as part of deploying an application
- 2 they should not be part of deploying an application
- 3 they should be specific to each environment
- 4 they should not be enabled during deployment

The correct answer is:

**✅ 1 — they should be deployed and enabled as part of deploying an application**

Services that are specific to an application need to be **deployed and activated together with the application** to ensure the application functions correctly and consistently across environments.

**11 which of following is true about a codebase**

- 1 it can be forked for different deployment
- 2 different deployment may have different codebase
- 3 it must be singular even it different have configuration that apply to them
- 4 env specific code be included in the codebase

The correct answer is:

**✅ 1 — it can be forked for different deployments**

A codebase can be **forked** (copied and modified) to support different deployments or versions when needed.

Explanation for others:

* **2** is usually avoided because having multiple different codebases for deployments leads to complexity and maintenance challenges.
* **3** is ideal in many cases—keeping a single codebase with environment-specific configs—but not always possible.
* **4** is discouraged; environment-specific logic should be handled via configuration, not hardcoded in the codebase.

**12 what is the importance of maintaining links in a codebase**

- 1 it allows for forking of the codebase
- 2 it ensures that different deployment thave different codebases
- 3 it defines the codebase and ensures it remains singular
- 4 it allows for env specfic code to be included in the codebase

The correct answer is:

**✅ 3 — it defines the codebase and ensures it remains singular**

Maintaining links (like references, dependencies, and version control connections) helps keep the codebase unified and coherent, preventing fragmentation and ensuring all changes are tracked and integrated properly.


**13 what does the enterprise do with collected infiormation

- 1 it use to maintain it's current security posture
- 2 it use to improve its security  posture dynamically
- 3 it uses it to improve  its security  posture statically

The correct answer is:

**2 — it use to improve its security posture dynamically**

Enterprises leverage collected information to **continuously and adaptively enhance** their security measures, responding in real-time or near-real-time to emerging threats and vulnerabilities.


**14 how is access to individual enterprise resources granted**

- 1 on an atomic basis
- 2 on a large basis
- 3 on a case-by-case basis
- 4 on a device-by-device basis

The correct answer is:

**✅ 1 — on an atomic basis**

Access to individual enterprise resources is granted at the **finest, smallest possible level (atomic basis)** to ensure precise control and minimize security risks by limiting permissions strictly to what is necessary.

**15 what is the attribute of an independent service**

- 1 being stateful
- 2 being able to share state with other services
- 3 being able to predict its halting externally
- 4 being stateless

The correct answer is:

**✅ 4 — being stateless**

An independent service is typically **stateless**, meaning it does not rely on storing state internally between requests, which makes it more scalable, resilient, and easier to manage independently.


**16 what is required for publishing events**

- 1 must have a defined schema and be published using a registered topic
- 2 must have a defined schema and published using an unregistered topic
- 3 they do not havea  defined schema
- 4 they can be published without using topic

The correct answer is:

**✅ 1 — must have a defined schema and be published using a registered topic**

This ensures events are well-structured, consistent, and discoverable, enabling reliable communication and integration across systems.

**17 what must be clear when cloud services scale with demand**

- 1 the cost of scaling 
- 2 the root cause of the demand
- 3 the number of users
- 4 the type of service

The correct answer is:

**✅ 1 — the cost of scaling**

When cloud services scale with demand, it’s crucial to clearly understand **how much scaling will cost** to manage budgets and optimize resource usage effectively.


**18 how are cloud services charged?**

- 1 to the users of the service
- 2 to the developer of service
- 3 to the owner of service
- 4 to the admin  of service

The correct answer is:

**✅ 3 — to the owner of service**

Cloud services are typically charged **to the service owner** who is responsible for managing costs, budgeting, and optimizing usage.
