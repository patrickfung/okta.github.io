---
layout: blog_post
title: "The Hitchhiker’s Guide to Testing Java APIs and Angular Components"
author: [mraible,bdemers]
description: "Building an app with Spring Boot and Angular is easy, but maintaining without tests can be difficult. This post shows you how to test your API and UI components."
tags: [testing, junit, spring-boot, angular, jasmine, protractor]
tweets:
 - "If you're not writing tests, you're testing in production. 🛑 and learn how to test your @springboot and @ionicframework apps >"
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
  
I like to call this a hitchhiker's guide because you can jump into any section of this guide and just learn how to test those particular components. If you have any suggestions for improving a particular section, please let us know so we can improve it! We try to keep our blog posts up-to-date as much as possible.

To begin, you'll want to clone the GitHub repository that contains the Ionic PWA and Spring Boot API.

```bash
git clone https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk.git
```

### Setup Okta

Log in to your Okta Developer account (or [sign up](https://developer.okta.com/signup/) if you don’t have an account).

The first thing you’ll need to do is add a `holdings` attribute to your organization’s user profiles. Log in to the Okta Developer Console, then navigate to **Users** > **Profile Editor**. Click on **Profile** for the first profile in the table. You can identify it by its Okta logo. Click **Add Attribute** and use the following values:

* Display name: `Holdings`
* Variable name: `holdings`
* Description: `Cryptocurrency Holdings`

You will need to create an API Token and OIDC App to get values to perform authentication and store data in Okta.

Navigate to **Applications** > **Add Application**. Click **Single-Page App**, click **Next**, and give the app a name you’ll remember. Click **Done**.

For the Okta Java SDK to talk to Okta’s API, you’ll need to create an API token. The abbreviated steps are as follows:

1. Log in to your Developer Console
2. Navigate to **API** > **Tokens** and click **Create Token**
3. Give your token a name, then copy its value

#### Server Configuration

Open `holdings-api/src/main/resources/application.properties` and add your API token as a property. While you're there, set the `issuer` and `clientId` to match your OIDC application.

**NOTE:** The value of `{yourOktaDomain}` should be something like `dev-123456.oktapreview.com`. Make sure you don't include `-admin` in the value!

```properties
okta.oauth2.issuer=https://{yourOktaDomain}.com/oauth2/default
okta.oauth2.clientId={yourClientId}
okta.client.token=XXX
```

> **NOTE:** If you don't want these values specified in your source code, you can set them as environment variables. For example, `OKTA_CLIENT_TOKEN` is the name of the environment variable that will override the `okta.client.token` property. See Spring Boot's [externalized configuration documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) to learn more.

#### Client Configuration

For the client, set the `issuer` and copy the `clientId` into `src/pages/login/login.ts`.

```typescript
const config = {
  issuer: 'https://{yourOktaDomain}.com/oauth2/default',
  redirectUri: window.location.origin + '/implicit/callback',
  clientId: '{clientId}'
};
```

## Testing Spring Boot Apps

### Unit Testing Spring Boot Controllers

### Mocking Okta's Java SDK with Mockito

### Mocking Okta's API with WireMock

## Testing Ionic Apps

Ionic 3.x is built on Angular. This means that you should be able to use Angular testing tools to verify your app works as expected. When I create Angular apps, I tend to use [Angular CLI](https://cli.angular.io). Angular CLI has a testing framework (Jasmine) built-in, as well as facilities to test via the command line or in a continuous integration server. If you're interested in learning more about how to write tests for an Angular app, I invite you to visit the [testing section](https://github.com/mraible/ng-demo#testing) of my Angular CLI tutorial. Tip: it's easier on the eyes to [read the testing tutorial on DocGist](http://gist.asciidoctor.org/?github-mraible/ng-demo//README.adoc#_testing).

Ionic CLI doesn't contain a `test` command like Angular CLI does. To solve this problem, I turned to Daniel Sogl's [ionic-super-starter](https://github.com/danielsogl/ionic-super-starter). It uses [Jest](https://facebook.github.io/jest/) for unit tests, mocks for many Ionic classes, and has end-to-end testing support too.

I learned how to add testing support to an Ionic app by creating an app with Daniel's starter:

```bash
ionic start ionic-super-starter danielsogl/super
```

Then, I copied the configuration into the `crypto-pwa` project. I've abbreviated those steps below.


### Unit Testing Ionic Components with Jest

### Driving and Testing Your UI with Protractor

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