---
title: 'Assumptions and Conditional Test execution, ignoring in JUnit'
date: 2018-07-07 07:40:20
tags:
---

Sometime you want to ignore some test depends on environment settings. Now both JUnit 4 and JUnit 5 support the concept of assumptions.

Unit Test like this:

``` Java
@Test
public void testRun() {
 
   Assume.assumeTrue(BooleanUtils.toBoolean(System.getProperty("runtest")));

   // Rest of testcase
}
```

With Gradle, pass in parameter like this:

``` Bash
$ gradle test -Pruntest=false
```


References
----------

- Assumptions and Conditional Test Execution with JUnit 4 and 5, https://reflectoring.io/conditional-junit4-junit5-tests/