---
type: architecture
---
# Design Patterns

## Software Architecture

Documnent to read:
- https://orkhanscience.medium.com/software-architecture-patterns-5-mins-read-e9e3c8eb47d2

### Layered architecture

For example: [[MVC]], [[3 Layers]], [[MVT]] of Django.

### Event-driven architecture

One of the most [[Distributed asynchronous architecture patterns]]

### Microkernel architecture

(or [[Plugin architecture]])

Mikrokernel Architecture, also known as [[Plugin architecture]], is the design pattern with two main components: a core system and plug-in modules (or extensions). A great example would be a Web browser (core system) where you can install endless extensions (or plugins).

### Microservices architecture

Microservices architecture consists of separately deployed services, where each service would have ideally [[single responsibility]]. Those services are independent of each other and if one service fails others will not stop running.

### Space-based architecture

(or [[Cloud architecture pattern]])

The main idea behind the space-based pattern is the distributed shared memory to mitigate issues that frequently occur at the database level. The assumption is that by processing most of operations using in-memory data we can avoid extra operations in the database, thus any future problems that may evolve from there (for example, if your user activity data entity has changed, you don’t need to change a bunch of code persisting to & retrieving that data from the DB).