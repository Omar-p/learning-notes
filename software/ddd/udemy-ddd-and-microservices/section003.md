- DDD approach provides principles and patterns to address the challenges faced with developing a complex domain model.
  - IT project's primary focus is on the core domain and domain logic.
  - Break a unified model into smaller more manageable models.
  - Iterative improvements to the model to solve a domain problem.

- Strategic Design:
  - Focus on the big picture of the domain.
  - Identify core domain, supporting subdomains, and generic subdomains.
  - Define bounded contexts to encapsulate models and ensure clear boundaries.
  - Establish context maps to define relationships between bounded contexts.
- Tactical Design:
  - Translate of the conceptual model into code; app, service design e.g. classes, modules.
  - Application within bounded context.


### Sub-Domain:
- each one has a different level of complexity
  - Business rules:
    - 
  - Compliance:
    -
  - Complex Calculation:
    - specialized knowledge
  - Process handover
    - 
  - Dynamicity:
    - if subdomain requires changes to process, rules, structure ; it will be diffcult to manage the knowledge and understanding of the domain.  
  - other factor depending on business.
- subdomains can be divided further into smaller sub-domains (level of granularity depends on complexity)
#### Categories of Sub-Domains:
- Core Domain:
  - most critical to the business success.
  - high complexity.
  - requires deep domain knowledge.
  - focus of development efforts.
  - differentiates the business from competitors.
- Supporting Sub-Domain:
  - supports the core domain.
  - less complex.
  - can be outsourced or off-the-shelf solutions.
  - not a source of competitive advantage.
- Generic Sub-Domain:
  - common across multiple industries.
  - low complexity.
  - can be handled by standard solutions or frameworks.
  - not specific to the business.
