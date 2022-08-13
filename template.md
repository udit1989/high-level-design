# HLD Design template
 
1. Getting to know the requirements  [3-5 mins]
    - Get to know the functional requirements, the scope and other objectives.
    - Try to list down a few functional requirements from your end, not so many, just the core.
    - And work on with the interviewer what all components you wish to implement.
    - Even in the case the requirements are provided, iterate through and ask questions.
        - ## Functional Requirements
        - ## Non-Functional Requirements
2. Getting to know the scale of the system
    - ## Back of envelope
    - This will become one of the major points to pick SQL vs. NoSQL
3. Discuss Storage capacity
    - What is persisted what is not.
    - Is the system Read heavy or Write heavy.
4. Design Decisions
    - CAP theorem: Choose Consistency(High or eventual) or Availability(High or eventual)
    - Latency: High latency or moderate latency.
5. Design for a Single system
    - Discuss the DB schema & Tables
        Preferably make the SQL tables & relations
    - Develop APIs on top of the DB
    - Discuss the business logic(the core of how things will work)
6. Talk about the scalability
    - SQL to NoSQL schema transformation
    - The High availability arch.
    - Talk about shards and other aspects to scale.
7. Present the high-level model for the same.

