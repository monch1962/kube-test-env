[![Gitpod ready-to-code](https://img.shields.io/badge/Gitpod-ready--to--code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/monch1962/kube-test-env)

# kube-test-env
Building a test environment on Kubernetes

## Overview
This repository is designed to capture information for building/maintaining on-demand test environments, as well as supporting infrastructure

### Generic test infrastructure
There is a generic set of supporting infrastructure for any test environment...

Key test infrastructure elements are:
- (optional) a test management system, to capture/store/present test cases & test results. Generally the type of system to be used will be pre-ordained; test infrastructure will be connecting to a pre-existing system. Examples would include Jira/XRay, ALM, TestRail
- (optional) a test results alerting mechanism, which can be used to publish test case execution results to any interested parties. Examples may include Microsoft Teams, Slack, email or SMS
- a system to automatically send test results to the test management system and/or test results alerting mechanism (refer to https://github.com/monch1962/junit2testrail for an example)
- storage space to temporarily hold test execution results, until they are processed into the test management system. This would be mounted as a Kubernetes volume so that test execution results can be created and processed by separate systems. A flexible, standard, non tool-specific format such as jUnit is ideal for capturing test results
- a mechanism for stubbing downstream dependencies of the system being tested
- the system being tested
- (optional) a mechanism for generating and/or loading data into the system being tested, to support test case execution
- a "testrunner" system for executing test cases against the system being tested. For functional API testing, this could be a container running Postman/Newman, Karate; for functional UI testing, this could be a container running Playwright or one of the various Selenium test runners; for load testing, this could be a container running Artillery, Gatling, k6 or JMeter
- a test code management system. Typically this would be a git repository of some sort, but it could also conceivably be a system such as TestRail
- (optional) a Pact broker, designed to hold API contract tests
- (optional) a mechanism to convert Pacts into executable test cases and stub configurations. An example would be Pactical (https://github.com/monch1962/pactical)
- a CI/CD pipeline, to trigger on-demand test environment creation, execution of test cases, and destruction of the on-demand test environment after testing has completed

### Generic automation test architecture
A generic functional automation environment architecture is as follows ...


### Generic performance test infrastucture
A generic performance test environment architecture is as follows...

## Test infrastructure build steps (long lived)
- create a Kubernetes volume to store test execution results until they are processed
- configure the test results alerting mechanism to update the test results alerting mechanism and/or the test management system as new test execution results become available
- (optional) create a Kubernetes namespace "test-data-generation", deploy a test data generation solution to this namespace, and expose it as a service so it can be consumed from other test namespaces
- deploy and configure a CI/CD toolset to execute test cases as necessary

## Test environments (on demand) 
### Test environment build steps
- create a new Kubernetes namespace for the test environment
- set up a stubbing mechanism for the test environment in separate pod/s
- set up any public cloud mocks in separate pod/s
- set up the system being tested in separate pod/s, and link it to the stubbing mechanism and public cloud mocks
- populate any required data into the system being tested
- create a "test-cases" volume
- generate and/or pull the test cases to be executed from the test case repository, and store them in the "test-cases" volume
- set up the "test runner" system/s in separate node/s, and mount the "test-cases" and "test-results" volumes to the "test runner" system/s

### Execution steps
- execute test cases on the "test runner" pod/s, reading test cases from the "test-cases" volume and writing test results to the "test-results" volume

### Test environment tear-down
- delete the Kubernetes namespace for the test environment
