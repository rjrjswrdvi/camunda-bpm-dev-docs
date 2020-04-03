# How to do a good review?

## Formal things

* Are the changes backported to all the necessary branches?
* Is the fix version set for all backported versions?
* Are the PR build results ok?
  * Did the necessary builds run?
  * Did the executed builds pass?
* Is the technical specification correct? Does it satisfy product requirements?

## Compatibility

### API
* Does the code keep internal concepts and classes out of the public API?
* Are the changes to the API consistent with the rest of the API?
* Are the changes breaking current public API?
* Is the changed API maintainable in future releases? 
  * Is the API potentially extensible for future use cases we can already anticipate? 
  * Does the API expose functionality that is not strictly necessary and that at the same time may be hard to maintain?
* Is the changed API easy to use for the user?
* If the code extends the Java API (e.g. adding a new parameter to a query, or a new property to a response), is this also added to the REST API?

### Backwards Compatibility
* Is the API [backwards compatible](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/Backwards-Compatibility.md)?
  * Do the changes affect [binary backwards compatibility](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/Backwards-Compatibility.md#binary-backwards-compatibility)
  * Is the API behavior still the same as before ([behavioral backwards compatibility](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/Backwards-Compatibility.md#behavioral-backwards-compatibility))? Is this ensured with tests?
* Is the database schema [backwards compatible](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/Backwards-Compatibility.md#database-schema-backwards-compatibility)?
* Do the changes break [rolling upgrade](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/concepts/Rolling-Upgrade.md) scenarios?

## Code
* Did we build the right thing? Does the implemented solution solve the problem?
* Do the changes adhere to the [coding style](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/Coding-Style-Java.md) guide?
* Do the changes contain commented out code?
* Are there any implementation bugs? (For example check for resource leaks or thread safety)
* Are the changes covered by tests? Can we improve the test coverage with reasonable effort?
* Is the code too complex?
  * Is there a simpler solution to solve the same problem?
  * Does the code contain meaningful comments for more complex parts?

## Things to watch out for
* Are critical and non-trivial design decisions documented in the ticket so that we will be able to understand in the future why we made them?
* Whenever the Java API is adjusted we need to make sure we perform authorization and tenant checks.
* Changes to the database schema require a new entry in the schema log (See [SQL and DDL Management](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/development/SQL-and-DDL-Management.md)).
* Operations that are performed by a logged in user often require entries to the user [operation log](https://docs.camunda.org/manual/latest/user-guide/process-engine/history/#glossary-of-operations-logged-in-the-user-operation-log).
* Is the feature well documented? For example:
  * [User Guide](https://docs.camunda.org/manual/latest/user-guide/)
  * [API reference](https://docs.camunda.org/manual/latest/reference/rest/)
  * Javadoc on public API
  * [Migration guide](https://docs.camunda.org/manual/latest/update/minor/) for database schema changes and API breaking changes

# Assigning a reviewer

Choosing the right person to review the changes should be based on:
* Knowledge of touched concepts
  * How much special knowledge is required?
  * Who has knowledge in that specific field?
* Current workload distribution in team.
* Knowledge Sharing
  * Assigning reviewers that have no particular knowledge in a specific field yet opens a chance for them to look into new parts of the code base.
  * Make sure the reviewer has all the information needed to review the code.

# Communication

* As implementing developer
  * Make sure to put all information that are necessary to review the feature into the ticket. Especially the technical requirements and how they cover the product requirements should be very clear.
  * Offer an introduction if you think the reviewer could need it (complex topic, knowledge sharing).
* As reviewing developer
  * Ask for introduction/clarification if necessary information are missing or something is not clear to you.

# Review effort

The assigned reviewer can request that the implementer chooses a different reviewer (e.g. due to current workload etc.).
If committed to review, the reviewer must make sure to do it properly with regard to checklist.
The reviewer should make sure to perform every step of the checklist or answer for themself why a specific step is not necessary.
