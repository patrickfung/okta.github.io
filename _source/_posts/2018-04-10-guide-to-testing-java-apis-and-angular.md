---
layout: blog_post
title: "git "
author: [mraible,bdemers]
description: "Building an app with Spring Boot and Angular is easy, but maintaining without tests can be difficult. This post shows you how to test your API and UI components."
tags: [testing, junit, spring-boot, angular, jasmine, protractor]
tweets:
 - "If you're not writing tests, you're testing in production. 🛑 and learn how to test your @springboot and @angular apps >"
 - "This testing guide shows you some techniques for testing your Java and Angular components when using @okta >"
---

I'll admit, writing tests is difficult to do. Writing the test often isn't difficult, it's just code after all. However, writing a *test first* is a habit that's tricky to form. However, if you do test-driven development (TDD), chances are that you'll develop a nicer API. After all, your test will act as a client to your API, and you'll see what code looks like for your users.

A couple of months ago, I wrote posts about developing a [cryptocurrency wealth tracking PWA](/blog/2018/01/18/cryptocurrency-pwa-secured-by-okta) and [storing your holdings as Okta custom profile attributes](/blog/2018/01/23/replace-local-storage-with-okta-profile-attributes). To show you how to write tests for the back-end and front-end components, I recruited [Brian Demers](https://twitter.com/briandemers) to help me. Brian is the lead developer on Okta's Java SDKs and a fun guy to hang out with.

In this tutorial, you'll see how to implement many aspects of testing a modern application:

* Unit testing the `HoldingsController` with JUnit
* Mocking Okta's Java SDK to return expected results with Mockito
* Mocking Okta's API with WireMock for integration tests
* Unit testing Angular components with Jasmine
* Driving and testing your UI with Protractor
* Continuous Integration and Delivery
  * Travis CI
  * Jenkins
  
I like to call this a hitchhiker's guide because you can jump into any section of this guide and just learn how to test those particular components. If you have any suggestions for improving a particular section, please let us know so we can improve it!

## Unit Testing Spring Boot Controllers

## Mocking Okta's Java SDK with Mockito

## Mocking Okta's API with WireMock

## Unit Testing Angular Components with Jasmine

## Driving and Testing Your UI with Protractor

## Continuous Integration and Delivery

### Travis CI

### Jenkins

## Code Coverage Tools

Why? 
Tracking over time

### JaCoCo Java Code Coverage Library

https://github.com/jacoco/jacoco

### Istanbul 

https://istanbul.js.org/

## Learn More

We hope you've enjoyed this hitchhiker's guide to testing Java and TypeScript applications. It's a really good feeling when your codebase has high test coverage and you're able to refactor by tweaking your code and adjusting your tests.