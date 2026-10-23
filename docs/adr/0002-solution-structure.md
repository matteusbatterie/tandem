# Record architecture decisions

- **Status**: Accepted
- **Date**: 2026-10-22

## Context

Tandem should follow principles to ensure maintainability, scalability, and testability of the codebase without depending on Trello or GitHub.

We should be able to easily add new connectors/webhooks in the future without affecting the core logic of Tandem.

## Decision

For the project structure and phylosophy, we'll follow the principles of Clean Architecture and Domain-Driven Design (DDD). The project will be structured into the following layers:

- **Domain Layer**: Contains the core business logic and domain entities. This layer is independent of any external frameworks or libraries.
- **Application Layer**: Contains the application services that orchestrate the use cases of the system. This layer depends on the domain layer but is NOT dependent of any external frameworks or libraries.
- **Infrastructure Layer**: Contains the implementation details for external systems, such as databases, APIs, and messaging systems. This layer depends on the application and domain layers.

APIs and Workers hosts will have their own projects/folders and will depend on the same Application and Infrastructure Layers.

Each Connector will have its own Worker project, so that we can easily add new connectors in the future without affecting the core logic of Tandem.

## Consequences

Boundaries are enforced by the project structure, which will help maintain a clean separation of concerns and make it easier to test and maintain the codebase.

On the other hand, we'll inevitably have more projects and boilerplates for a small codebase, and two processes or more to run locally.
