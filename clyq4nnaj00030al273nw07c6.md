---
title: "Continuous testing for java - Keppler - Discovery"
datePublished: Wed Jul 17 2024 17:39:47 GMT+0000 (Coordinated Universal Time)
cuid: clyq4nnaj00030al273nw07c6
slug: continuous-testing-for-java-keppler-discovery
tags: java, terminal, testing, dotnet

---

<mark>This article is updated continuously.</mark>

In the recent weeks I did some discovery of what tools look good enough to build the Keppler software. This is its name: Keppler.

# Terminal UI

The terminal will be done by [Terminal.GUI](https://github.com/gui-cs/Terminal.Gui). The plan is that this UI will communicate with the Server responsible executing the test cases.

# Server

The server will be a Dotnet Core server, probably ends up being gRPC, but for building a POC it will be a REST one.

Responsibility of the Server are the following:

* parsing the output of Junit's ConsoleLauncher and process it for the UI
    
* executing build whenever the code changes in order to reflect the changes on the UI, meaning if the developer adds a new test and the code can compile the new test should be displayed as soon as possible and with zero interaction from the developer
    

# Test case discovery

Test case discovery happens by Junit's standalone ConsoleLauncher and the Server will process the output. The ConsoleLauncher has multiple output option and the `verbose` mode looks like the most feasible.

Test case discovery provides the information to display the test case tree. The following cases will demonstrate what capabilities the whole system will have.

## **Scenario:** Tests are organised by packages

[These](https://github.com/GeishaCoffee/JunitTest/tree/main/JunitTest/src/test/java/com/andrascsanyi/junittest/organisedbypackages) test cases organised by package and nothing else.

**Command**:

```bash
java -jar junit-platform-console-standalone-1.10.3.jar discover -cp 'target/test-classes/' --disable-banner --details=verbose --select-package=com.andrascsanyi.junittest.organisedbypackage
```

**Result**:

```bash
├─ JUnit Jupiter
│  ├─ P1Tests
│  │  ├─ p1Test1()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p1.P1Tests', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ P1Tests
│  ├─ P2Tests
│  │  ├─ p2Test1()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]/[method:p2Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests', methodName = 'p2Test1', methodParameterTypes = '']
│  │  ├─ p2Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]/[method:p2Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests', methodName = 'p2Test2', methodParameterTypes = '']
│  │  ├─ p2Test3()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]/[method:p2Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.organisedbypackages.p2.P2Tests', methodName = 'p2Test3', methodParameterTypes = '']
│  └─ P2Tests
└─ JUnit Jupiter
├─ JUnit Vintage
│       tags: []
│   uniqueId: [engine:junit-vintage]
│     parent: []
├─ JUnit Platform Suite
│       tags: []
│   uniqueId: [engine:junit-platform-suite]
│     parent: []
```

**Conclusion**: it shows enough information to build the tree

## **Scenario:** There are disabled test cases

[These](https://github.com/GeishaCoffee/JunitTest/tree/main/JunitTest/src/test/java/com/andrascsanyi/junittest/disabled) tests include one disabled test.

**Command**:

```bash
java -jar junit-platform-console-standalone-1.10.3.jar discover -cp 'target/test-classes/' --disable-banner --details=verbose --select-package=com.andrascsanyi.junittest.disabled
```

Result:

```bash
├─ JUnit Jupiter
│  ├─ IncludesDisabledTests
│  │  ├─ p1Test1()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.disabled.IncludesDisabledTests', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.disabled.IncludesDisabledTests', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.disabled.IncludesDisabledTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.disabled.IncludesDisabledTests', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ IncludesDisabledTests
└─ JUnit Jupiter
├─ JUnit Vintage
│       tags: []
│   uniqueId: [engine:junit-vintage]
│     parent: []
├─ JUnit Platform Suite
│       tags: []
│   uniqueId: [engine:junit-platform-suite]
│     parent: []
```

**Conclusion**: it is not marked which test is disabled. Other solution is needed to be able to acquire the information which test is enabled and disabled and provide this information to the UI.

## Scenario: there are class level tags

One of the test classes [here](https://github.com/GeishaCoffee/JunitTest/tree/main/JunitTest/src/test/java/com/andrascsanyi/junittest/classleveltags) have a single tag while the other one has multiple tags.

**Command**:

```bash
java -jar junit-platform-console-standalone-1.10.3.jar discover -cp 'target/test-classes/' --disable-banner --details=verbose --select-package=com.andrascsanyi.junittest.classleveltags
```

**Result**:

```bash
├─ JUnit Jupiter
│  ├─ ClassLevelTagsTests
│  │  ├─ p1Test1()
│  │  │       tags: [simpletag]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: [simpletag]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: [simpletag]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTagsTests', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ ClassLevelTagsTests
│  ├─ ClassLevelTags2Tests
│  │  ├─ p1Test1()
│  │  │       tags: [multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: [multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: [multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classleveltags.ClassLevelTags2Tests', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ ClassLevelTags2Tests
└─ JUnit Jupiter
├─ JUnit Vintage
│       tags: []
│   uniqueId: [engine:junit-vintage]
│     parent: []
├─ JUnit Platform Suite
│       tags: []
│   uniqueId: [engine:junit-platform-suite]
│     parent: []
```

**Conclusion**: when class level tags are added they won't be displayed in the output as class level tags, rather added to every test method. From filtering point of view this is what makes sense. If the UI is going to display tags in any shape or form the processing has to deal with finding the class level tags and separate them from the method level tags.

## Scenario: there are method level tags

[These](https://github.com/GeishaCoffee/JunitTest/tree/main/JunitTest/src/test/java/com/andrascsanyi/junittest/methodleveltags) classes have methods having a single tag and multiple tags.

**Command**:

```bash
java -jar junit-platform-console-standalone-1.10.3.jar discover -cp 'target/test-classes/' --disable-banner --details=verbose --select-package=com.andrascsanyi.junittest.methodleveltags
```

**Result**:

```bash
├─ JUnit Jupiter
│  ├─ MethodLevelTagTest
│  │  ├─ p1Test1()
│  │  │       tags: [p1Test1-tag]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagTest', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ MethodLevelTagTest
│  ├─ MethodLevelTagsTest
│  │  ├─ p1Test1()
│  │  │       tags: [methodTag1, methodTag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: [methodTag3, methodTag4]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.methodleveltags.MethodLevelTagsTest', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ MethodLevelTagsTest
└─ JUnit Jupiter
├─ JUnit Vintage
│       tags: []
│   uniqueId: [engine:junit-vintage]
│     parent: []
├─ JUnit Platform Suite
│       tags: []
│   uniqueId: [engine:junit-platform-suite]
│     parent: []
```

**Conclusion**: tags are displayed at the methods

## Scenario: there are class and method level tags

[These](https://github.com/GeishaCoffee/JunitTest/tree/main/JunitTest/src/test/java/com/andrascsanyi/junittest/classandmethodleveltags) classes have method and class level tags.

Command:

```bash
java -jar junit-platform-console-standalone-1.10.3.jar discover -cp 'target/test-classes/' --disable-banner --details=verbose --select-package=com.andrascsanyi.junittest.classandmethodleveltags
```

Result:

```bash
├─ JUnit Jupiter
│  ├─ MethodLevelTagsTest
│  │  ├─ p1Test1()
│  │  │       tags: [doesnthaveclasstag1, onlyemethodtag]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: []
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: [doesnthaveclasstag2, onlyemethodtag1]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.MethodLevelTagsTest', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ MethodLevelTagsTest
│  ├─ ClassLevelTags2Tests
│  │  ├─ p1Test1()
│  │  │       tags: [hasclasstags, multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]/[method:p1Test1()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests', methodName = 'p1Test1', methodParameterTypes = '']
│  │  ├─ p1Test2()
│  │  │       tags: [multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]/[method:p1Test2()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests', methodName = 'p1Test2', methodParameterTypes = '']
│  │  ├─ p1Test3()
│  │  │       tags: [hasclasstags2, multitag1, multitag2]
│  │  │   uniqueId: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]/[method:p1Test3()]
│  │  │     parent: [engine:junit-jupiter]/[class:com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests]
│  │  │     source: MethodSource [className = 'com.andrascsanyi.junittest.classandmethodleveltags.ClassLevelTags2Tests', methodName = 'p1Test3', methodParameterTypes = '']
│  └─ ClassLevelTags2Tests
└─ JUnit Jupiter
├─ JUnit Vintage
│       tags: []
│   uniqueId: [engine:junit-vintage]
│     parent: []
├─ JUnit Platform Suite
│       tags: []
│   uniqueId: [engine:junit-platform-suite]
│     parent: []
```

**Conclusion**: class level tags are added to the methods of the class and if the methods have their own tags they are added to the tree too.

Filtering by package

Filtering by tag

## Executing tests

The test execution happens with Junit's ConsoleLauncher.

**Expectations**:

* Test case discovery
    
    * can return a list of tests
        
        * includes meta information like class name, method name, suite name and so on, so it is possible to build the test case tree
            
* Test case execution
    
    * can run a single test
        
    * can run multiple test cases across multiple classes