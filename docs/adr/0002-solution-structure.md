# 0002. Solution structure

- **Status**: Accepted
- **Date**: 2026-10-22

## Context

Tandem should follow principles to ensure maintainability, scalability, and testability of the codebase without depending on Trello or GitHub.

I should be able to easily add new connectors/webhooks in the future without affecting the core logic of Tandem.

## Decision

For the project structure and philosophy, we'll follow the principles of Clean Architecture and Domain-Driven Design (DDD). The project will be
structured into the following layers:

- **Domain Layer**: Contains the core business logic and domain entities. This layer is independent of any external frameworks or libraries.
- **Application Layer**: Contains the application services that orchestrate the use cases of the system. This layer depends on the domain layer but is
NOT dependent on any external frameworks or libraries.
- **Infrastructure Layer**: Contains the implementation details for external systems, such as databases, APIs, and messaging systems. This layer
depends on the application and domain layers.

APIs and Workers hosts will have their own projects/folders and will depend on the same Application and Infrastructure Layers.

Each connector is a separate class library that implements a port defined in the Application layer. The API and Worker hosts
reference the connectors and register them at startup, so adding a connector means adding a project and a registration, without
changing the Domain or Application layers.

## Alternatives Considered

I could have only one host that runs the Worker inside the API process, or one project with folders inside instead of separate projects for each Connector.

This would've reduced complexity and overhead, probably more suited for the small scope of this presentation.

However, acknowledging a webhook fast is a different job from slow work that may fail and retry, so they shouldn't share a process. On top of that, since the main point of this repository
is to display and exercise knowledge, I opted for the more complete approach.

## Consequences

Boundaries are enforced by the project structure, which will help maintain a clean separation of concerns and make it easier to test and maintain the codebase.

On the other hand, we'll inevitably have more projects and boilerplate for a small codebase, and two processes or more to run locally.
