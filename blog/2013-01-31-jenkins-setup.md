# Setting up Jenkins for a JavaScript project

Jenkins is a fairly nice self-hosted CI server, fit for most uses. It's not perfect though: UI is rather ugly reminiscent of the early 00s, it has enterprisey feeling due to its Java focus and dependency, and it can be resource hog at times; but nevertheless Jenkins is easy to setup, it is very flexible and has plugins for most needs.

In this tutorial I'll show an example on how to setup Jenkins as CI server for our JavaScript app, which consists of API written using Node.js and frontend using Backbone.js. 

Let’s skip the basic Jenkins setup, there’s plenty of tutorials for that. Come back after you've installed Jenkins and configured basics such as security (e.g. [role-based](http://wiki.jenkins-ci.org/display/JENKINS/Role+Strategy+Plugin) strategy is quite simple to setup).

### Required Plugins:
For this tutorial you need the following plugins:

- [GitHub plugin](http://wiki.jenkins-ci.org/display/JENKINS/Github+Plugin)
- [Jenkins Cobertura Plugin](http://wiki.jenkins-ci.org/display/JENKINS/Cobertura+Plugin)
- [Jenkins TAP Plugin](http://wiki.jenkins-ci.org/display/JENKINS/TAP+Plugin)
- [Jenkins Violations plugin](http://wiki.jenkins-ci.org/display/JENKINS/Violations)
- [Jenkins Email Extension Plugin](http://wiki.jenkins-ci.org/display/JENKINS/Email-ext+plugin)
- [HTML Publisher plugin](http://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)
- [Checkstyle Plug-in](https://wiki.jenkins-ci.org/display/JENKINS/Checkstyle+Plugin)
- [Join plugin](http://wiki.jenkins-ci.org/display/JENKINS/Join+Plugin)
- [Flexible Publish Plugin](http://wiki.jenkins-ci.org/display/JENKINS/Flexible+Publish+Plugin)


## Create a new job

Creating a new job is simple:
- Click 'New Job' link on the left.
- Usually you'll want to choose *Build a free-style software project* or *Copy existing Job* in order to copy configuration from an existing job.
- Give it a descriptive name in *Job name* text input. It's good to have a naming convention e.g. *project-name-job-description*. For example 'admin-client-unit-tests'

[img]

### Configure basics

- Fill in the project name and description.
- Then type the repo's GitHub address into *GitHub project*
- Under *Source Code Management* tick *Git* and type the repo's ssh address
- For configuring GitHub push with service hooks read [this](http://blog.cloudbees.com/2012/01/better-integration-between-jenkins-and.html) and [this](http://fourkitchens.com/blog/2011/09/20/trigger-jenkins-builds-pushing-github)
- If you tick *Build when a change is pushed to GitHub*, job is built after a commit has been pushed to GitHub. Usually you'll want to trigger job only on changes in master branch, so type 'master' in *Branch Specifier*

[img]

## Build actions
Now you have a job that correctly pulls changes from GitHub after each commit, but it doesn't yet do anything - so it's time add a build step

- Click *Add build step* -button. Choose *Execute shell*

In this example we want to add a build step to run our unit tests. It's project dependent how you run your tests, but I recommend keeping most of the logic out of Jenkins e.g. in GNU Makefile. Also choose what kind of output your test runner generates. For example [TAP](http://en.wikipedia.org/wiki/Test_Anything_Protocol) is very simple format with support in many good test runners. As an example the build step could be the following:
    
    mkdir -p reports
    make test REPORTER=tap > reports/test_results.tap
    
This would first create *reports* folder under the job's workspace for outputting all test results (and it's good to add this folder to *.gitignore* for running tests locally).

Now we should be able to run our unit test suite successfully. You can test the build by saving the job and clicking *Build Now* - button.

## Post-build actions
Post-build actions mean stuff that you do after job's build steps have been completed successfully or failed. For example we would like display test results in the job's dashboard and email developer if tests failed.

- click *Add post-build step* and choose *Publish TAP results*
- Type in the path for test results' tap file
- click *Add post-build step* and choose *E-mail notification* choose *Send separate e-mails to individuals who broke the build* and make sure you've configured developers email addresses in user management.

[img]

## Code coverage reports
There's many different code coverage plugins for Jenkins, but if found the Cobertura plugin and [Istanbul](https://github.com/gotwarlost/istanbul) to be the nicest fit for our use. We using Mocha testing framework, so we need [mocha-istanbul](https://github.com/arikon/mocha-istanbul) to generate coverage report with mocha.

Here's the relevant parts from our makefile

    BIN = ./node_modules/.bin
    UNIT_TESTS = $(shell find test -name '*.test.js')
    INSTRUMENTATION_OUTPUT = build/lib-cov
    REPORTS = reports

    instrument: clean-coverage
        $(BIN)/istanbul instrument --output $(INSTRUMENTATION_OUTPUT) --no-compact \ 
            --variable global.__coverage__ lib

    coverage: instrument
        @ISTANBUL_REPORTERS=html,text-summary,cobertura EXPRESS_COV=1 \
            $(BIN)/mocha --bail --reporter mocha-istanbul $(UNIT_TESTS)
        $(MAKE) move-reports

    move-reports:
        -mkdir -p reports
        -mv cobertura-coverage.xml reports
        -cp -r html-report reports/
        -rm -rf html-report

    clean-coverage:
        -rm -rf $(INSTRUMENTATION_OUTPUT)
        -rm -rf $(REPORTS)


As you see we are generating both cobertura report and html report. Html report is needed because cobertura plugin doesn't provide very good drill down view all the way until code level. We want to be able to see in each code file which code lines get executed. Istanbul's html reports provide that, but you need to add a separate post-build job for viewing the results in Jenkins:

- click *Add post-build step* and choose *Publish HTML reports* put 'reports/html-report' as directory to archive.

[img]

## Static code analysis & style check

We want to use JSHint to check our code for errors and style. We are running JSHint before our unit tests run, so in case of error it's spotted on unit testing build step. You can also view JSHint results in Jenkins using *Report violations* plugin with:

    $(BIN)/jshint --config $(JSHINT_CONFIG) \
        --jslint-reporter \
        $(SRC_FILES) \
        > reports/jslint.xml || true

This is best done as a separate build step, where you call above makefile target and then create a post-build step with *Report violations* plugin and put the jslint pattern in place.

You can also check style with:

    $(BIN)/jshint --config $(JSHINT_CONFIG) \
        --checkstyle-reporter \
        $(SRC_FILES) \
        > reports/checkstyle-jshint.xml || true
        
And view reports with *Publish Checkstyle analysis results* post-build step.

## Publishing custom images

If you're running browser tests, it might be a good idea to take screenshots on failed builds to make it easier to find out where build failed or just to check that layout looks correct. To do that you might want to use Flexible Publish Plugin:

- click *Add post-build step* and choose *Flexible publish*
- choose *Archive the artifacts* and fill the path where images get saved ('reports/*.png' in our case)

## Handling project dependencies

Our example project actually consists of several different smaller projects: we have multiple backends that provide APIs for our frontend code. We want to be able to run our end-to-end tests against real server so we have to make sure that when code is pushed in the dependent repos, we deploy the newest code in our test server and run our end-to-end tests.

Jenkins has a *Join plugin*, which helps you decouple your jobs into smaller sub-jobs. You can use it to add post-build step *Build other projects*, where you can specify jobs to run after current job is finished. It also has *Join trigger* for specifying jobs that are built after jobs specified in 'Build other projects' are done.

### Example test flow
We have the following jobs:

####test-suite
- gets triggered after GitHub push
- only acts as mediator for triggering other builds
- triggers jobs: unit-tests & deploy

####unit-tests
- job for running unit tests
- handles reporting for failures

####deploy
- deploys the app and triggers job e2e-tests after done

####e2e-tests
- job for running end-to-end-tests
- end-to-end tests are run against server specified in 'deploy-api'

####deploy-api
- deploys backend API code to test server after GitHub commit

There's no bi-directional dependencies in our case so if API code gets changed, it doesn't trigger test flow for client code. It would be possible to add this in similar way though, if needed.
