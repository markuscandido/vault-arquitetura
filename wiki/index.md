# Wiki Index

Catalog of all knowledge pages.

## Sources
- [[PEAA_Book]]: Patterns of Enterprise Application Architecture (Martin Fowler).

## Entities
*No entities defined yet.*

## Concepts
- [[Enterprise_Applications]]: Characteristics and challenges of enterprise systems.
- [[Architecture]]: Subjective definition and focus in software development.
- [[Layering]]: Fundamental decomposition technique.
    - [[Presentation_Layer]]
    - [[Domain_Layer]]
    - [[Data_Source_Layer]]

## Patterns
### Presentation
- [[MVC|Model-View-Controller (MVC)]]: Fundamental separation of UI and Logic.
## Patterns
### Domain Logic
- [[Transaction_Script]]: Procedural organization of logic.
- [[Domain_Model]]: Object-oriented organization of logic.
- [[Table_Module]]: Table-oriented organization of logic.
- [[Service_Layer]]: Application boundary and coordination.


### Data Source Mapping
- [[Table_Data_Gateway]]: One object per table for all records.
- [[Row_Data_Gateway]]: One object per database record.
- [[Active_Record]]: Record object that includes business logic.
- [[Data_Mapper]]: Independent layer for object-relational mapping.

### Object-Relational Metadata & Behavior
- [[Unit_of_Work]]: Managing database transactions.
- [[Identity_Map]]: Ensuring object uniqueness.
- [[Lazy_Load]]: On-demand data loading.
- [[Identity_Field]]: Mapping primary keys to objects.

### Concurrency & Infrastructure
- [[Optimistic_Offline_Lock]]: Version-based conflict detection.
- [[Pessimistic_Offline_Lock]]: Lock-based conflict prevention.
- [[Session_State]]: Managing user interaction state.
- [[Distribution_Strategies]]: Why (not) to distribute objects.

## Strategy & Synthesis
- [[Synthesis]]: Guidelines for choosing the right patterns.
