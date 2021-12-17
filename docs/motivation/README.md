---
sidebar: auto

---

# Motivation

There are many tools to script performance/load tests, being [JMeter](http://jmeter.apache.org/) and [Gatling](https://gatling.io/) the most popular ones.

## Alternatives analysis

### JMeter

JMeter is great for people with no programming knowledge since it provides a graphical interface to create test plans and run them. Additionally, it is the most popular tool (with a lot of supporting tools built on it) and has a big amount of supported protocols and plugins that makes it very versatile.

But, JMeter has some problems as well: sometimes might be slow to create test plans in JMeter GUI, and you can't get the full picture of the test plan unless you dig in every tree node to check its properties. Furthermore, it doesn't provide a simple programmer friendly API (you can check [here](https://www.blazemeter.com/blog/5-ways-launch-jmeter-test-without-using-jmeter-gui/) for an example on how to run JMeter programmatically without jmeter-java-dsl), nor a Git friendly format (too verbose and hard to review). For example, for this test plan:

```java
import static org.assertj.core.api.Assertions.assertThat;
import static us.abstracta.jmeter.javadsl.JmeterDsl.*;

import java.io.IOException;
import java.time.Duration;
import java.time.Instant;
import org.apache.http.entity.ContentType;
import org.junit.jupiter.api.Test;
import us.abstracta.jmeter.javadsl.core.TestPlanStats;

public class PerformanceTest {

  @Test
  public void testPerformance() throws IOException {
    TestPlanStats stats = testPlan(
      threadGroup(2, 10,
        httpSampler("http://my.service")
          .post("{\"name\": \"test\"}", ContentType.APPLICATION_JSON)
      ),
      //this is just to log details of each request stats
      jtlWriter("test" + Instant.now().toString().replace(":", "-") + ".jtl")
    ).run();
    assertThat(stats.overall().sampleTimePercentile99()).isLessThan(Duration.ofSeconds(5));
  }
  
}
```

In JMeter, you would need a JMX file like [this](../../docs/motivation/sample.jmx), and even then, it wouldn't be as simple to do assertions on collected statistics as in provided example.

### Gatling

Gatling does provide a simple API and a Git friendly format, but requires scala knowledge and environment. Additionally, it doesn't provide as rich environment as JMeter (protocol support, plugins, tools) and requires learning a new framework for testing (if you already use JMeter, which is the most popular tool).

### Taurus

[Taurus](https://gettaurus.org/) is another open-source tool that allows specifying tests in a Git friendly yaml syntax, and provides additional features like pass/fail criteria and easier CI/CD integration. But, this tool requires a python environment, in addition to the java environment. Additionally, there is no built-in GUI or IDE auto-completion support, which makes it harder to discover and learn the actual syntax. Finally, Taurus syntax only supports a subset of the features JMeter provides, which reduces scope usage.

### ruby-dsl

Finally, [ruby-dsl](https://github.com/flood-io/ruby-jmeter) is also an opensource library which allows specifying and run in ruby custom dsl JMeter test plans. This is the most similar tool to jmeter-java-dsl, but it requires ruby (in addition to java environment) with the additional performance impact, does not follow same naming and structure convention as JMeter, and lacks of debugging integration with JMeter execution engine.

### jmeter-java-dsl

jmeter-java-dsl tries to get the best of these tools by providing a simple java API with Git friendly format to run JMeter tests, taking advantage of all JMeter benefits and knowledge also providing many of the benefits of Gatling scripting.
As shown in previous example, it can be easily executed with JUnit, modularized in code and easily integrated in any CI/CD pipeline. Additionally, it makes it easy to debug the execution of test plans with usual IDE debugger tools. Finally, as with most Java libraries, you can use it not only in a Java project, but also in projects of most JVM languages (like kotlin, scala, groovy, etc.).

## Comparison Table

Here is a table with summary of main pros and cons of each tool:

|Tool|Pros|Cons|
|----|----|----|
|JMeter| 👍 GUI for non programmers<br/>👍 Popularity<br/>👍 Protocols Support<br/>👍 Documentation<br/>👍 Rich ecosystem|👎 Slow test plan creation<br/>👎 No VCS friendly format<br/>👎 Not programmers friendly<br/>👎 No simple CI/CD integration|
|Gatling| 👍 VCS friendly<br/>👍 IDE friendly (auto-complete and debug)<br/>👍 Natural CI/CD integration<br/>👍 Natural code modularization and reuse<br/>👍 Less resources (CPU & RAM) usage<br/>👍 All details of simple test plans at a glance|👎 Scala knowledge and environment required<br/>👎 Smaller set of protocols supported<br/>👎 Less documentation & tooling|
|Taurus| 👍 VCS friendly<br/>👍 Simple CI/CD integration<br/>👍 Unified framework for running any type of test<br/>👍 built-in support for running tests at scale<br/>👍 All details of simple test plans at a glance<br/>👍 Simple way to do assertions on statistics|👎 Both Java and Python environments required<br/>👎 Not as simple to discover (IDE auto-complete or GUI) supported functionality<br/>👎 Not complete support of JMeter capabilities (nor in the roadmap)|
|ruby-dsl| 👍 VCS friendly<br/>👍 Simple CI/CD integration<br/>👍 Unified framework for running any type of test<br/>👍 built-in support for running tests at scale<br/>👍 All details of simple test plans at a glance|👎 Both Java and Ruby environments required<br/>👎 Not following same naming convention and structure as JMeter<br/>👎 Not complete support of JMeter capabilities (nor in the roadmap)<br/>👎 No integration for debugging JMeter code|
|jmeter-java-dsl| 👍 VCS friendly<br/>👍 IDE friendly (auto-complete and debug)<br/>👍 Natural CI/CD integration<br/>👍 Natural code modularization and reuse<br/>👍 Existing JMeter documentation<br/>👍 Easy to add support for JMeter supported protocols and new plugins<br/>👍 Could easily interact with JMX files and take advantage of JMeter ecosystem<br/>👍 All details of simple test plans at a glance<br/>👍 Simple way to do assertions on statistics|👎 Basic Java knowledge required<br/>👎 Same resources (CPU & RAM) usage as JMeter|
