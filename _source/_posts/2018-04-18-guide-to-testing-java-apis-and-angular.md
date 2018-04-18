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
git clone https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example.git
```

The instructions below assume you're operating from the `okta-ionic-crypto-java-sdk-example` directory of this cloned project.

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

**NOTE:** The value of `{yourOktaDomain}` should be something like `dev-123456.oktapreview`. Make sure you don't include `-admin` in the value!

```properties
okta.oauth2.issuer=https://{yourOktaDomain}.com/oauth2/default
okta.oauth2.clientId={yourClientId}
okta.client.token=XXX
```

> **NOTE:** If you don't want these values specified in your source code, you can set them as environment variables. For example, `OKTA_CLIENT_TOKEN` is the name of the environment variable that will override the `okta.client.token` property. See Spring Boot's [externalized configuration documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) to learn more.

#### Client Configuration

For the client, set the `issuer` and copy the `clientId` into `crypto-pwa/src/pages/login/login.ts`.

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

1. Add scripts in the `scripts` section of your `package.json` to run unit tests and end-to-end (e2e) tests:

    ```json
    "test": "jest",
    "test:watch": "jest --watch",
    "test:ci": "jest --runInBand",
    "test:coverage": "jest --coverage",
    "e2e": "npm run e2e-update && npm run e2e-test",
    "e2e-test": "protractor ./test/protractor.conf.js",
    "e2e-update": "webdriver-manager update --standalone false --gecko false"
    ```

2. Add the following dependencies to the `devDependencies` section of `package.json`:

    ```json
    "typescript": "2.8.1",
    "@types/jest": "^22.2.3",
    "@types/node": "^9.6.5",
    "html-loader": "^0.5.1",
    "ionic-mocks-jest": "^1.0.3",
    "istanbul-instrumenter-loader": "^3.0.0",
    "jasmine": "^3.1.0",
    "jasmine-spec-reporter": "^4.1.0",
    "jest": "^22.4.3",
    "jest-preset-angular": "^5.2.1",
    "null-loader": "^0.1.1",
    "protractor": "^5.1.1",
    "ts-loader": "^4.2.0",
    "ts-node": "^6.0.0"
    ```

3. Add configuration for Jest, on same level as `devDependencies` and `scripts` in `package.json`:

    ```json
    "jest": {
      "preset": "jest-preset-angular",
      "setupTestFrameworkScriptFile": "<rootDir>/test/jest.conf.ts",
      "transformIgnorePatterns": [
        "node_modules/(?!@ngrx|@ionic-native|@ionic)"
      ],
      "coveragePathIgnorePatterns": [
        "<rootDir>/e2e/"
      ],
      "collectCoverageFrom": [
        "**/*.{ts}",
        "!**/*d.{ts}",
        "!**/*interface.{ts}",
        "!**/*module.{ts}",
        "!**/*main.{ts}",
        "!**/node_modules/**"
      ],
      "moduleFileExtensions": [
        "ts",
        "js",
        "json"
      ],
      "coverageReporters": [
        "json",
        "lcov",
        "text"
      ],
      "coverageDirectory": "./public/coverage"
    }
    ```

4. Create a `crypto-pwa/test` directory and create two files in it: `jest.conf.ts` and `protractor.conf.js`.

    ```ts
    // test/jest.conf.ts
    import 'jest-preset-angular';
    ```

    ```js
    // test/protractor.conf.js
    const { SpecReporter } = require('jasmine-spec-reporter');

    exports.config = {
      allScriptsTimeout: 11000,
      specs: [
        '../e2e/**/*.e2e-spec.ts'
      ],
      capabilities: {
        'browserName': 'chrome'
      },
      directConnect: true,
      baseUrl: 'http://localhost:8100/',
      framework: 'jasmine',
      jasmineNodeOpts: {
        showColors: true,
        defaultTimeoutInterval: 30000,
        print: function() {}
      },
      onPrepare() {
        require('ts-node').register({
          project: 'e2e/tsconfig.e2e.json'
        });
        jasmine.getEnv().addReporter(new SpecReporter({ spec: { displayStacktrace: true } }));
      }
    };
    ```

5. Create `src/tsconfig.spec.json` and populate it with the following JSON:

    ```json
    {
      "extends": "../tsconfig.json",
      "compilerOptions": {
        "outDir": "../out-tsc/spec",
        "module": "commonjs",
        "target": "es5",
        "allowJs": true
      },
      "include": [
        "**/*.spec.ts"
      ],
      "exclude": [
        "**/*e2e-spec.ts"
      ]
    }
    ```

6. Create `e2e/tsconfig.e2e.json` and configure its settings:

    ```json
    {
      "extends": "../tsconfig.json",
      "compilerOptions": {
        "outDir": "../out-tsc/e2e",
        "baseUrl": "./",
        "module": "commonjs",
        "target": "es5",
        "typeRoots": [
          "../node_modules/@types"
        ]
      }
    }
    ```

**NOTE:** After making all these changes, I updated all dependencies in `crypto-pwa/package.json` by installing [npm-check-updates](https://www.npmjs.com/package/npm-check-updates) and running `ncu -u`.

### Unit Testing Ionic Components with Jest and Jasmine

According to the [Jest](https://facebook.github.io/jest/) homepage, it's used by Facebook to test all JavaScript code including React applications. Jest strives for zero-configuration, but you can tell from the files above that it still requires some configuration. It does have built-in code coverage reports, which is kinda cool.

MR: the following code blog might work better at the end.

> If you're new to Jest and want to learn the basics, I invite you to read its [Getting Started](https://facebook.github.io/jest/docs/en/getting-started.html) guide.

Jest is a test runner, similar to [Karma](https://karma-runner.github.io). Both runners support using [Jasmine](https://jasmine.github.io) in your tests. For Java developers, Jasmine is the JUnit of the JavaScript ecosystem. A simple Jasmine tests looks as follows:

```js
describe('A suite is just a function', () => {
  let a;

  it('and so is a spec', () => {
    a = true;

    expect(a).toBe(true);
  });
});
```

**TIP:** If you want to only run specific tests, you can add an `f` prefix to your `describe` or `it` functions, so they become `fdescribe` or `fit`. If you want to exclude tests, you can add an `x` prefix to these same code blocks (e.g., `xdescribe` and `xit`).

After making the changes above, you have several options to run unit tests:

1. Run `npm test` to execute all the unit tests.
1. Run `npm run test:watch` to execute tests and watch for changes. If anything changes, tests are automatically executed.
1. Run `npm test:ci` to execute tests in a continuous integration (CI) server. This runs all tests serially in the current process, rather than creating a worker pool of child processes that run tests.
1. Run `npm run test:coverage` to generate a test coverage report.

These commands are great to know, but only useful if you have unit tests!

Create `crypto-pwa/src/app/app.component.spec.ts` and populate it the following code:

```ts
import { async, TestBed } from '@angular/core/testing';
import { IonicModule, Platform } from 'ionic-angular';

import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

import { PlatformMock, SplashScreenMock, StatusBarMock } from 'ionic-mocks-jest';

import { MyApp } from './app.component';

describe('MyApp Component', () => {
  let fixture;
  let component;
  let oauthService = {
    hasValidIdToken() {
      return false;
    }
  };

  beforeEach(
    async(() => {
      TestBed.configureTestingModule({
        declarations: [MyApp],
        imports: [IonicModule.forRoot(MyApp)],
        providers: [
          {provide: StatusBar, useFactory: () => StatusBarMock.instance()},
          {provide: SplashScreen, useFactory: () => SplashScreenMock.instance()},
          {provide: Platform, useFactory: () => PlatformMock.instance()},
          {provide: OAuthService, useFactory: () => oauthService}
        ]
      });
    })
  );

  beforeEach(() => {
    fixture = TestBed.createComponent(MyApp);
    component = fixture.componentInstance;
  });

  it('should be created', () => {
    expect(component instanceof MyApp).toBe(true);
  });

  it('should show login page', () => {
    expect(component.rootPage).toEqual('LoginPage');
  });
});
```

This test will pass, but there's a lot going on, so let me break it down for you. First of all, you might notice there's a number of imports from `ionic-mocks-jest`. If generate an Ionic app using `ionic start` and select the "tabs" starter, you'll have a `MyApp` component that looks as follows:

```ts
export class MyApp {
  rootPage:any = TabsPage;

  constructor(platform: Platform, statusBar: StatusBar, splashScreen: SplashScreen) {
    platform.ready().then(() => {
      // Okay, so the platform is ready and our plugins are available.
      // Here you can do any higher level native things you might need.
      statusBar.styleDefault();
      splashScreen.hide();
    });
  }
}
```

Whenever a component has dependencies defined in its constructor, you need to "provide" those in your component's test.

To make it easy to components that depend on Ionic components, [Daniel Sogl](https://twitter.com/sogldaniel) forked the [ionic-mocks](https://github.com/stonelasley/ionic-mocks) project to create [ionic-mocks-jest](https://github.com/danielsogl/ionic-mocks-jest). This project provides a number of mock objects using [Jasmine Spy Objects](https://jasmine.github.io/2.0/introduction.html#section-Spies), with support for Jest. A spy can stub any function and tracks calls to it, as well as all the arguments passed in. You can imagine how this can be useful, especially when testing.

The `crypto-pwa` project is meant to a be a PWA, not a native app, so these dependencies don't exist in its `MyApp` component. However, I thought I'd point them out in case your app uses them.

To test an Angular component, there's a bit of boilerplate code. If I were to simplify the test above to only have the essentials for a bare-bones component and test, it'd look like the following:

```ts
describe('MyApp Component', () => {
  let fixture;
  let component;

  beforeEach(
    async(() => {
      TestBed.configureTestingModule({
        declarations: [MyApp]
      });
    })
  );

  beforeEach(() => {
    fixture = TestBed.createComponent(MyApp);
    component = fixture.componentInstance;
  });

  it('should show login page', () => {
    expect(component.rootPage).toEqual('LoginPage');
  });
});
```

This test will fail because the `MyApp` component loads `app.html` with Ionic elements in it

```
 FAIL  src/app/app.component.spec.ts
  MyApp Component
    ✕ should be created (647ms)
    ○ skipped 1 test

  ● MyApp Component › should be created

    Template parse errors:
    Can't bind to 'root' since it isn't a known property of 'ion-nav'.
    1. If 'ion-nav' is an Angular component and it has 'root' input, t
```

Import `IonicModule` and you'll get a bit further. This is common pattern. If you have custom components in your templates, you'll need to import their modules in your tests, just like you do in `app.module.ts`.

```ts
beforeEach(
  async(() => {
    TestBed.configureTestingModule({
      declarations: [MyApp],
      imports: [IonicModule.forRoot(MyApp)]
    });
  })
);
```

If you try to run the test now, it'll complain that the constructor dependency in `MyApp` is not available.

```
StaticInjectorError(DynamicTestModule)[MyApp -> OAuthService]:
  StaticInjectorError(Platform: core)[MyApp -> OAuthService]:
    NullInjectorError: No provider for OAuthService!
```

To fix this, create a mock instance of `oauthService` and specify it as a provider.

```ts
const oauthService = {
  hasValidIdToken() {
    return false;
  }
};

beforeEach(
  async(() => {
    TestBed.configureTestingModule({
      declarations: [MyApp],
      imports: [IonicModule.forRoot(MyApp)],
      providers: [
        {provide: OAuthService, useFactory: () => oauthService}
      ]
    });
  })
);
```

You only need to define the `hasValidIdToken()` in `oauthService` because that's the only method that's used in `MyApp`:

```ts
import { Component } from '@angular/core';
import { OAuthService } from 'angular-oauth2-oidc';

@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage: any = 'HomePage';

  constructor(oauthService: OAuthService) {
    if (oauthService.hasValidIdToken()) {
      this.rootPage = 'HomePage';
    } else {
      this.rootPage = 'LoginPage';
    }
  }
}
```

Now you can create a test that ensures the login page is shown when no ID token exists.

```ts
it('should show login page', () => {
  expect(component.rootPage).toEqual('LoginPage');
});
```

Run `npm test` and everything should pass!

```
> jest

 PASS  src/app/app.component.spec.ts
  MyApp Component
    ✓ should be created (633ms)
    ✓ should show login page (544ms)

Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        4.232s
Ran all test suites.
```

### Driving and Testing Your UI with Protractor

Protractor recommends [using Page Objects to organize tests](https://github.com/angular/protractor/blob/master/docs/page-objects.md). Page Objects help you write cleaner tests by encapsulating information about a page's elements in a single object.

Setup a basic e2e test of your application's homepage with the following steps:

1. Create a generic `Page` object at `crypto-pwa/e2e/pages/app.po.ts`:

    ```ts
    import { browser } from 'protractor';

    export class Page {

      navigateTo(destination) {
        return browser.get(destination);
      }

      getTitle() {
        return browser.getTitle();
      }
    }
    ```

2. Create `crypto-pwa/e2e/spec/app.e2e-spec.ts` and define a "default screen" test suite:

    ```ts
    import { Page } from '../pages/app.po';
    import { browser, protractor } from 'protractor';

    describe('App', () => {
      let page: Page;

      beforeEach(() => {
        page = new Page();
      });

      describe('default screen', () => {
        beforeEach(() => {
          page.navigateTo('/#/home');
        });

        it('should redirect to login', () => {
          const EC = protractor.ExpectedConditions;
          browser.wait(EC.urlContains('/#/login'), 5000);
        });

        it('should have the correct title', () => {
          page.getTitle().then(title => {
            expect(title).toEqual('Cryptocurrency PWA with Authentication');
          });
        });
      });
    });
    ```

**TIP:** Using this same pattern, you could create a more specific `HomePage` class that allows you to navigate to it, get its title, and locate its login button. This could simplify the test above a bit, but not by much.

```ts
import { browser, by, element } from 'protractor';

export class HomePage {

  getPage() {
    return browser.get('/#/home');
  }

  getPageTitle() {
    return browser.getTitle();
  }

  getLoginButton() {
    return element(by.id('login'));
  }
}
```

**NOTE:** In order for `getLoginButton()` to work, you'll need to add `id="login"` to the login button in `crypto-pwa/src/pages/login/login.html`.

To execute Protractor tests, run `ionic serve` in one terminal and `npm run e2e` in another. The (silent) video below shows what should happen.

<div style="width: 600px; margin: 0 auto">
<object width="600" height="338"><param name="movie" value="https://www.youtube.com/v/MO_ZWxI7Yi4&hl=en&fs=1"></param><param name="allowFullScreen" value="true"></param><embed src="https://www.youtube.com/v/MO_ZWxI7Yi4&hl=en&fs=1" type="application/x-shockwave-flash" allowfullscreen="true" width="600" height="338"></embed></object>
</div>

## Continuous Integration and Delivery

Having tests is great, but you know what's better? Running them every time a pull request (PR) is created for your project. You do use Git to store your projects in source don't you?! I'll assume you do. I mean, I know you're a smart developer if you're reading this. Friends don't let friends write authentication, and you're here so you don't have to, right? ;)

Two of the most populate CI servers are [Travis CI](https://travis-ci.org/) and [Jenkins](https://jenkins.io/), so we'll cover how to use those.

### Travis CI

If you've checked your project in to [GitHub](https://github.com/), you can use Travis CI.

1. Log in to [Travis CI](https://travis-ci.org/) and enable builds for the GitHub repo you published the project to.
2. Add the following `.travis.yml` in your root directory, create a branch for it, and `git commit/push` it. This will trigger the first build.

    ```yaml
    os:
      - linux
    services:
      - docker
    language: node_js
    node_js:
      - "9.8.0"
    addons:
      apt:
        sources:
        - google-chrome
        packages:
        - google-chrome-stable
    jdk:
      - oraclejdk8
    sudo: false
    cache:
      directories:
        - crypto-pwa/node_modules
    before_install:
      - jdk_switcher use oraclejdk8
      - java -version
      - export CHROME_BIN=/usr/bin/google-chrome
      - export DISPLAY=:99.0
      - sh -e /etc/init.d/xvfb start
      - npm install -g ionic@3.20.0
    script:
      - chmod +x holdings-api/mvnw
      - cd holdings-api && ./mvnw clean test
      - cd ../crypto-pwa && npm test
      - cd ../holdings-api/mvnw spring-boot:run &
      - cd ../crypto-pwa && ionic serve
      - npm run e2e
    notifications:
      webhooks:
        on_success: always
        on_failure: always
        on_start: false
    ```

This script will run your Spring Boot tests with Java 8, run the Jest tests, start the backend, start the frontend, and then run Protractor tests to verify everything works. You can see a successful build using this script in the screenshot below.

// TODO: Add image

### Jenkins

A Jenkins script defines different stages for testing your application in Jenkins. Put the code below in a `Jenkinsfile` file at the root of your application, and check it in to source control.

```groovy
#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java and node') {
        sh "java -version"
        sh "node -version"
    }

    stage('clean') {
        sh "chmod +x ./holdings-api/mvnw"
        sh "./holdings-api/mvnw clean"
    }

    stage('install tools') {
        sh "cd crypto-pwa && npm install"
    }

    stage('backend tests') {
        try {
            sh "./holdings-api/mvnw test"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/**/TEST-*.xml'
        }
    }

    stage('frontend tests') {
        try {
            sh "cd crypto-pwa && npm test"
        } catch(err) {
            throw err
        }
    }

    stage('protractor tests') {
        sh '''./holdings-api/mvnw spring-boot:run &
        bootPid=$!
        sleep 60s
        cd crypto-pwa && npm run e2e
        kill $bootPid
        '''
    }

    stage('packaging') {
        sh "./holdings-api/mvnw package"
        sh "cd crypto-pwa && npm run ionic:build --prod"
        archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        archiveArtifacts artifacts: '**/www/**', fingerprint: true
    }
}
```

**TIP:** If you want to add JUnit reporting to your Jest tests, see [jest-junit](https://www.npmjs.com/package/jest-junit).

To test this application with Jenkins, I [downloaded](https://jenkins.io/download/) `jenkins.war` to `/opt/tools/jenkins` on my MacBook Pro. I started it with the following command.

```
java -jar jenkins.war --httpPort=9000
```

To log in to Jenkins, I navigated to `http://localhost:9000`. I copied the password from the startup log file and pasted
into the unlock Jenkins page.

<!-- MR: re-do these images with the 'demo' profile -->

{% img blog/java-testing-guide/unlock-jenkins.png alt:"Unlock Jenkins" width:"800" %}{: .center-image }

Next, I chose to install selected plugins and waited while everything installed.

{% img blog/java-testing-guide/customize-jenkins.png alt:"Customize Jenkins" width:"800" %}{: .center-image }

I created a new job called "Bootiful Crypto PWA" with a Pipeline script from SCM. I configured a "Poll SCM" build trigger with a schedule of `H/5 * * * *`. After saving the job, I confirmed it ran successfully.

// TODO: Add image

You might notice that I haven't mentioned continuous deployment. This can be added by simply adding a new stage to your `Jenkinsfile`, or adding more configuration to `.travis.yml`.

For example:

// Add example with Firebase (for Travis) and Cloud Foundry or Heroku (for Jenkins)

## Code Coverage Tools

Why? 
Tracking over time

### JaCoCo Java Code Coverage Library

https://github.com/jacoco/jacoco

### Istanbul 

https://istanbul.js.org/

## Learn More

We hope you've enjoyed this hitchhiker's guide to testing Java and TypeScript applications. It's a really good feeling when your codebase has high test coverage and you're able to refactor by tweaking your code and adjusting your tests.