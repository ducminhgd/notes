---
type: management
keywords:
- Software Engineering
- Process
---
# Feature-driven development

Feature-driven development (FDD) is an iterative and incremental software development process. It is a lightweight or agile method for developing software.

## History

FDD was initially devised by Jeff De Luca to meet the specific needs of a **15-month, 50-person software development project** at a large Singapore bank in 1997.

The description of FDD was first introduced to the world in Chapter 6 of the book *Java modelling in Color with UML* by Peter Coad, Eric Lefebvre, and Jeff De Luca in 1999. Later, in Stephen Palmer and Mac Felsing's book *A Practical Guide to Feature-Driven Development* (published in 2002), a more general description of FDD was given decoupled from Java modelling.

## Overview

![FDD Process Diagram](https://upload.wikimedia.org/wikipedia/commons/9/99/Fdd_process_diagram.png)

FDD is a model-driven short-iteration process that consists of five basic activities. For accurate state reporting and keeping track of the software development project, milestones that mark the progress made on each feature are defined. This section gives a high-level overview of the activities. In the figure on the right, the meta-process model for these activities is displayed. During the first two sequential activities, an overall model shape is established. The final three activities are iterated for each feature.

### Develop overall model

The FDD project starts with a high-level walkthrough of the scope of the system and its context. Next, detailed domain models are created for each modelling area by small groups and presented for peer review. One or more of the proposed models are selected to become the model for each domain area. Domain area models are progressively merged into an overall model.

### Build feature list

Knowledge gathered during the initial modeling is used to identify a list of features by functionally decomposing the domain into subject areas. Subject areas each contain business activities, and the steps within each business activity form the basis for a categorized feature list. Features in this respect are small pieces of client-valued functions expressed in the form "<action> <result> <object>", for example: 'Calculate the total of a sale' or 'Validate the password of a user'. Features should not take more than two weeks to complete, else they should be broken down into smaller pieces.

### Plan by feature

After the feature list is completed, the next step is to produce the development plan and assign ownership of features (or feature sets) as classes to programmers.

### Design by feature

A design package is produced for each feature. A chief programmer selects a small group of features that are to be developed within two weeks. Together with the corresponding class owners, the chief programmer works out detailed sequence diagrams for each feature and refines the overall model. Next, the class and method prologues are written, and finally a design inspection is held.

### Build by feature

After a successful design inspection for each activity to produce a feature is planned, the class owners develop code for their classes. After unit testing and successful code inspection, the completed feature is promoted to the main build.

## Best practices

Feature-driven development is built on a core set of software engineering best practices aimed at a client-valued feature perspective.

- Domain object modelling. Domain object modeling consists of exploring and explaining the domain of the problem to be solved. The resulting domain object model provides an overall framework in which to add features.
- Developing by feature. Any function that is too complex to be implemented within two weeks is further decomposed into smaller functions until each sub-problem is small enough to be called a feature. This makes it easier to deliver correct functions and to extend or modify the system.
- Individual class ownership (code ownership). Individual class ownership means that distinct pieces or grouping of code are assigned to a single owner. The owner is responsible for the consistency, performance, and conceptual integrity of the class.
- Feature teams. A feature team is a small, dynamically formed team that develops a small activity. Multiple minds are always applied to each design decision, and multiple design options are evaluated before one is chosen.
- Inspections. Inspections are carried out to ensure good quality design and code primarily by the detection of defects.
- Configuration management. Configuration management helps with identifying the source code for all features that have been completed to date and maintaining a history of changes to classes as feature teams enhance them.
- Regular builds. Regular builds ensure there is always an up-to-date system that can be demonstrated to the client and help highlight integration errors of source code for the features early.
- Visibility of progress and results. Managers steer a project using frequent, appropriate, and accurate progress reporting from all levels inside and outside the project based on completed work.

## Read more

1. [Wikipedia](https://en.wikipedia.org/wiki/Feature-driven_development)