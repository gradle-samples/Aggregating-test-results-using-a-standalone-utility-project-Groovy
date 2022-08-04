## Aggregating test results using a standalone utility project Groovy


NOTE: You can open this sample inside an IDE using the [IntelliJ native importer](https://www.jetbrains.com/help/idea/gradle.html#gradle_import_project_start) or [Eclipse Buildship](https://projects.eclipse.org/projects/tools.buildship).

This sample shows how to aggregate test results across multiple Java subprojects.  The link:{userManualPath}/test_report_aggregation_plugin.html[test-report-aggregation plugin] provides this ability via a standalone project used to specify _which_ subprojects to include for aggregation.

The project in this example contains three "code" subprojects: `application`, `list` and `utilities`.  All three projects apply the `java` plugin, and `application` consumes both `list` and `utilities` via its implementation configuration.  A fourth subproject `test-results` is the standalone utility project used to collect the aggregated test results.

```
The Test Report Aggregation plugin does not currently work with the ``com.android.application`` plugin.
```


The standalone project applies `test-report-aggregation`, but requires additional configuration if the `jvm-test-suite` plugin is not also present (it will be automatically applied by the `java` plugin).

In this scenario, two additional pieces of setup are necessary:

<1> Declare dependencies using the `testReportAggregation` configuration
<2> Define a report of type `AggregateTestReport` which collects test data from unit test suites
<3> Optional: make aggregate test report generation part of the 'check' lifecycle phase

The report aggregation logic does not automatically inspect all subprojects for test results to aggregate.  Instead, the direct and transitive <<declaring_dependencies.adoc#sub:project_dependencies,project dependencies>> of the `testReportAggregation` configuration are selected for potential aggregation.

The user must also declare one or more reports of type `AggregateTestReport`.  Each report instance specifies a `testType` property, used to match the test suite producing the test data.  A `TestReport` task is synthesized for each user-defined report and performs the aggregation.  Invoking this task will cause tests to be executed in the dependent projects of the `testReportAggregation` configuration.

Run the tests and generate the report:

```
$ ./gradlew testAggregateTestReport
```

```
BUILD SUCCESSFUL
24 actionable tasks: 24 executed
```

The aggregated HTML report can now be found under `test-results/build/reports/tests/unit-tests/aggregated-results`.

For more information, see [Testing in Java project chapter](https://docs.gradle.org/current/userguide/java_testing.html).
