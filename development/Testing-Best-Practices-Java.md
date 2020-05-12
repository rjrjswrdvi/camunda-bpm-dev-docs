Intro:

* These are our best practices around unit testing
* They should be followed when writing tests, but exceptions can be made

Best Practices:

* A test should include `//given`, `//when`, `//then` comments, so it is easy to see what is tested
```java
@Test
public void shouldCheckFoo() {
  // given
  <test setup>
  
  // when
  <trigger logic under test>
  
  // then
  <fetch result>  
  <assert result>
}
```
* Naming Conventions
  * [Camel case](https://en.wikipedia.org/wiki/Camel_case)
  * JUnit 4 tests are prefixed with "should", e.g. `shouldCheckComponentAvailability()`
* A test should not test multiple things, i.e. there can be only one when part
* The when part only triggers the code under test; Any lookup for results goes into the then part
* Query tests should assert the results, not only the count of results (exception: the query returns all results)
  * Relevant for all types of query tests (e.g., also for authorization query tests, etc.)
  * Count-based query tests are error-prone since they don't verify that the correct data is retrieved
  * The following AssertJ assertion verifies the right count as well as the correct data: 
    ```java 
    assertThat(query.list())
        .extracting("taskId")
        .containsExactlyInAnyOrder("taskIdOne", "taskIdTwo");
    ```
* If you need to scroll down to see the whole test, consider to break it down
* A test should always have at least one assertion
* Use fluent AssertJ assertions
* If possible, use JUnit 4 conventions (e.g. test rule, `ExpectedException` rule)
* Write blackbox tests based on public API
  * Avoid writing tests that rely on internal API (e.g. for preparing a certain state)
  * Avoid testing internal API
* Preferred Annotation order:
```java
@Ignore
@Test
@Deployment(resources = {"testProcess.bpmn"})
```
* For common test setup and tear down (e.g. services, deployments), use `@Before` and `@After` methods
* Use `@RequiredHistoryLevel` on method or class level, if a test requires a certain history level
