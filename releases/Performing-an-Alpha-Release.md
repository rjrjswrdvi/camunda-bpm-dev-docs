Process:

<img src="img/releases/alphaRelease.png" align="left" >


Steps:
- [ ] [Define release version](#define-release-version) (*)
- [ ] [Make calendar appointments](#make-calendar-appointments) (*)
- [ ] [Prepare the blogpost](#prepare-the-blogpost) (*)
- [ ] [Check Preconditions](#check-preconditions) (*)
- [ ] [Trigger the release build](#trigger-the-release-build) (*)
- [ ] [Update the Enterprise Download Page](#update-the-enterprise-download-page) (MS,*)
- [ ] [Test Release](#test-release) (MS, *)
- [ ] [Release Maven Central](#release-maven-central) (*)
- [ ] [Release the documentation (manual)](#release-the-documentation-manual) (*)
- [ ] [Release Javadocs](#release-javadocs) (*)
- [ ] [Release Jira](#release-jira) (*)
- [ ] [Forward Security Reports](#forward-security-reports) (*)
- [ ] [Update the Community Download Page](#update-the-community-download-page) (*)
- [ ] [Check Docker Images](#check-docker-images) (*)
- [ ] [Release the blogpost](#release-the-blogpost) (TL, *)
- [ ] [Publish the Enterprise Page](#publish-the-enterprise-page) (*)
- [ ] [Send Release Notice](#send-release-notice) (*)
- [ ] [Forum Announcement](#forum) (*)
- [ ] [Improve this guide](#improve-this-guide) (*)
- [ ] [Celebrate the release](#present-and-celebrate-the-release) (*)
- [ ] [Pick Next Release Manager](#pick-next-release-manager) (*)

Steps marked with (*) are to be performed by the "Release Manager".
MS = Michael Schöttes
TL = Thorben Lindhauer
RS = Roman Smirnov
CM = Charley Mann

# Define release version 
There are five alpha releases before every minor release named alpha1 to alpha5. Major, minor and patch version are the same as the next minor release. The format of the version is `<MAJOR>.<MINOR>.<PATCH>-<ALPHA>` (e.g. 7.9.0-alpha3, 7.12.0-alpha5, ...). More information can be found in the [release policy](https://docs.camunda.org/enterprise/release-policy/) in the docs.

This guide contains links and filters using placeholders. Please replace the placeholder with the corresponding substitute.

# Make calendar appointments

## Deciding on the release date
Decide upon the exact date for the release day. How to find the release day:
* it is always a Friday, except if the Friday is a holiday, then it's the day before (Thursday) and so on.
* take the last day of the month and find the Friday that is "closest" to that date. Closest means that you have to count the number of days from the last day of the month to the previous/next Friday.
* Ask the optimize, modeler and infrastructure teams if they want to participate in the release presentation

## **Release presentation**
The release presentation is done remotely via Zoom. Create a Zoom meeting for the presentation. Ask Backoffice for a pro account.
> 🕔 set a reminder on that date for the release presentation (~3 pm). Include the Zoom link in the invitation. Audience: alle@camunda.com. Location: none

> 🕔 book a room for the team where we can hold the presentation. Audience: camundabpm@camunda.com Location: Any room with enough space.

## **Blog post contribution**
> 🕔  set a reminder in the evening of the day before the code freeze so everyone has their blogpost contribution committed.
> Audience: camundabpm@camunda.com

## **Code Freeze**
> 🕔  in the evening of the day before the build will take place until its end. If the release presentation is on Friday, the code freeze can be scheduled for Wednesday evening.
> Audience: camundabpm@camunda.com

## **Build release**
> 🕗  as the duration of the build lasts at least two hours, make sure to trigger it early in the morning after the code freeze and keep in mind that it might fail. Make sure you have no other appointments on the day of the release.
> Audience: camundabpm@camunda.com, infra@camunda.com

## **Celebrate release**
> Please see [Celebrate The Release](#present-and-celebrate-the-release).

# Prepare the Blogpost

Goal: Prepare a blogpost skeleton on a remote branch, such that your colleagues can contribute their sections.


#### 1. Update the 'master' and create a branch on the [blog repository](https://github.com/camunda/blog.camunda.org).

```
git checkout master
git pull origin master
git checkout -b 7.9.0-alpha3
```

#### 2. Create a markdown file and push it to the repo.

The filename must follow the format `camunda-bpm-<release-version-short>-released.md` and must be located in `/content/post/<year>/<month>/`. (e.g. `camunda-bpm-790-aplha3-released.md`, `camunda-bpm-7120-alpha5-released.md`)
It should contain an introduction (feature overview, release notes, download encouragement) and conclusion (feedback encouragement, outlook). You can use the blogpost of the last alpha release as a blueprint.

```
git add /content/post/2017/07/camunda-bpm-790-alpha3-released.md
git commit
git push origin 7.9.0-alpha3
```

#### 3. Encourage Content

Ask your colleagues to contribute content for any noteworthy features they have implemented.

# Check Preconditions

- There are no Snapshot dependencies to secondary projects like Spin or Connect ([Release procedure](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/releases/Release-secondary-projects.md)). For that, check [bom](https://github.com/camunda/camunda-bpm-platform/blob/master/bom/pom.xml) for snapshot dependencies (All snapshot dependencies contain the word 'SNAPSHOT' in their name).
- There are no code problems ([check ci](https://broken.cambpm.camunda.cloud/) section "Master" and "Release")
- [Release Test Job](https://release.cambpm.camunda.cloud/view/Release-Test/) passed successfully recently
- Send an email to camundabpm@camunda.com:
```
Hey,

please stop pushing to master since we are going to build the alpha release.
```

# Trigger the Release Build

Open the following URL in Jenkins:

https://release.cambpm.camunda.cloud/view/Release-Master/

Click on the "Run" icon. Make sure to set the configuration to something like:

```
DEVELOPMENT_VERSION: 7.9.0-SNAPSHOT
RELEASE_VERSION:     7.9.0-alpha3
PUSH_REMOTE:         true
RELEASE_TYPE:        ALPHA
OVERRIDE_TAG:        false
SKIP_TESTS:          true
SKIP_DEPLOY:         false
BRANCH:              master
```

Click Build.
Wait for the following jobs to turn green before continuing with the next step (check e.g. [here](https://release.cambpm.camunda.cloud/view/Release-Master/builds)):


* 7.9-RELEASE-build-camunda-bpm-CE-tags
* 7.9-RELEASE-build-camunda-bpm-EE-tags

Please also check that there are no issues at the jenkins 'Release' build (see [here](https://broken.cambpm.camunda.cloud/)).

# Update the Enterprise Download Page

Add the latest release to the following page [`camunda-docs-static/enterprise/content/download.md`](https://github.com/camunda/camunda-docs-static/blob/master/enterprise/content/download.md).

All releases are handled as HUGO page variables in the document header. Add the release in the following format:

```
  branches:
  - branch: "<minor-version>"
    releases:
    - number: "<release-version>"
      note: "<link-to-blog-post>"
      date: "<release-date>"
```

Make sure that you have set the right minor version (e.g. 7.9) and release version (e.g. 7.9.0-alpha3).

# Release the Enterprise Page

Commit the changes and build the enterprise page:

```
git commit -m "chore(download): add 7.9.0-alpha3 to download page"

git push origin master
```

> Review your commit on http://stage.docs.camunda.org/enterprise/download/

> Make sure that the link to the release notes works as expected.


# Test Release

For each container (including Spring Boot & Run) one developer should perform a test. Download the release artifacts from http://stage.docs.camunda.org/enterprise/download/

## Preparations

* Make sure the Docker QA images are ready to use
  1. Check if the Jenkins Job was executed successfully (if not retrigger it)
     * You can find the job here: `https://ci.cambpm.camunda.cloud/view/all/job/<minor-version>/job/<minor-version>-platform-docker-qa/`
     * If the job continues to fail, you could try to build & push the images locally from your machine. Please see the documentation about [Manually Build & Push the Docker QA Images](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/howtos/manually-build-docker-qa-images.md)
  2. Promote the versions of the Docker images in the [Portainer Templates Repository](https://github.com/camunda-ci/portainer-templates).
     * You can find an example commit here: https://github.com/camunda-ci/portainer-templates/commit/d94033
  3. To confirm that everything works as expected, go to Portainer, run a container for each image, and check the "Stack Details". You should validate the right version is picked up, and the container is "runnable". Please see the following screenshot:\
   ![Portainer Stack Details](https://raw.githubusercontent.com/camunda/camunda-bpm-dev-docs/master/howtos/img/manually-build-docker-qa-images-portainer-stack-details.png)
     * The step requires a VPN connection. Find all the details here: https://confluence.camunda.com/display/ADMIN/VPN
* Provide a test plan sheet as a copy from [the template](https://docs.google.com/spreadsheets/d/1K9xRFix6NFjnFJDVailOYPTkzJyQCA9yIrFcyFtC3KE/edit#gid=1656336280),
make sure to prepare the server/database combinations to be tested before next step (Conduct Testing). Assign combinations of application servers and databases to devs. Make sure the major servers and database are tested once. It is not necessary to test multiple combinations of those.
For an alpha, the coverage of JDKs, browsers and operating systems is not so important, so this choice can be left to the devs conducting the test.

## Conduct Testing

1. Send a message in Slack (or an email to camundabpm@camunda.com) asking the team to participate in manual testing:
```
Hey team,

We are starting the release test for the current alpha. The server/database combination for testing are already defined in the test release sheet.

Once you are done with testing, please inform me of the result in this thread and I will assign the next environment if necessary.

You can download the distros here: http://stage.docs.camunda.org/enterprise/download/
Here is the test release spreadsheet: <link>
Perform the standard regression test: [Standard Regression Test](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/releases/Performing-an-Alpha-Release.md#standard-regression-test)

```
2. Don't forget to make transparent how the communication between testers and you should happen to prevent confusion and miscommunication.
3. Keep track environments and test results in the test plan sheet.

### Standard Regression Test
1. Download the release artifact from the enterprise download page
2. Combine the platform with a database of choice.
3. Start the platform
    1. Check the server log that no exceptions occur
4. Open Admin
    1. Check if the dashboard and main menu contain the sections 'Users', 'Groups', 'Tenants', 'Authorizations', 'System'
    2. Add a license key to the platform
5. Open Tasklist
    1. Start a new invoice process
    2. Walk through the invoice process step by step
6. Open Cockpit
    1. Use the dashboard search plugin to find all finished invoice instances (There should be at least one)
    2. Go to the runtime instance view of the invoice showcase
    3. Switch to the history view
    4. Migrate all instances from the invoice process version 1 to version 2
    5. Use the dashboard search plugin to find all instances started after a certain date
    6. Cancel these instances by using the batch operations
    7. Select a process instance and go to the process instance view
    8. Perform a process instance modification
    9. Add a variable to the process instance

#### Testing with Spring Boot
Use the spring-boot-example [example webapp ee](https://github.com/camunda/camunda-bpm-examples/tree/master/spring-boot-starter/example-webapp-ee) as test scenario.

Check out compatible versions for the release --> [Spring Boot Version Compatibility](https://docs.camunda.org/manual/develop/user-guide/spring-boot-integration/version-compatibility/).

Change the pom.xml file:
```
<properties>
  <camunda.version><release-version>-ee</camunda.version>
  <spring.boot.version>2.1.4.RELEASE</spring.boot.version>
  <camunda.spring.boot.starter.version>3.3.0-SNAPSHOT</camunda.spring.boot.starter.version>
</properties>
```


Enable Groovy Script Engine if necessary

```
<!-- https://mvnrepository.com/artifact/org.codehaus.groovy/groovy-jsr223 -->
<dependency>
    <groupId>org.codehaus.groovy</groupId>
    <artifactId>groovy-jsr223</artifactId>
    <version>2.4.13</version>
</dependency>
```

##### Using the Invoice example in Spring Boot

The Spring Boot Starter - Invoice example integrates the Invoice application with the Camunda Spring Boot Starter. It
can be found at [https://github.com/camunda/camunda-bpm-examples/tree/master/spring-boot-starter/example-invoice](https://github.com/camunda/camunda-bpm-examples/tree/master/spring-boot-starter/example-invoice).

The example adds the necessary JDBC dependencies, as well as the `application.yaml` configuration to run on one of
the Camunda-supported databases. By default, H2 will be used, while the rest can be un-commented. 

> Note: the Spring Boot Starter Invoice example will not create the expected Tasklist filters or additional users. If 
> necessary, these should be created manually through the `application.properties` and the Admin Webapp file.

### Release Specific Test

According to the implemented feature topics choose some of the new feature to to test them manually.
If you don't know wich fixes are worth the effort use the following rule of thumb:
* Prefer UI features as the test coverage is usually lower compared to backend features. Use different browser for the test.
* Prefer features related to customer support issues as our customers expect that these features are working correctly.

If you are satisfied with the release, send an email to camundabpm@camunda.com:

```
Hey,

the release test passed, you can commit to master again :)

```

# Release Maven Central

> Precondition release tests are passed.

We have several CI jobs which upload artifacts to Maven Central into their staging repository section. In order to make them publicly available, we need to manually close the staging repositories and release the artifacts.

1. Go to [Maven Central](https://oss.sonatype.org/) and login using the credentials found in our [Confluence](https://app.camunda.com/confluence/display/camBPM/Maven+Central+Release)
2. On the left side, click on the 'Staging Repositories' link.
3. After it has been loaded, scroll down to the bottom of the list. You should find the relevant Camunda staging repositories there.
4. Mark each repository you want to release.

    *For Example:*  

    >org.camunda.bpm:camunda-bom:7.9.0-alpha3  
    >org.camunda.bpm.webapp:camunda-webapp:7.9.0-alpha3

5. Click on 'Release' at the menu on top of the list. A window will pop up were you can enter a description but it is not necessary. Activate 'Drop repository after release automatically'. Then proceed.
6. Done.

Hint: It takes about two hours until the artifacts can be found on Maven Central.

# Release the Documentation (Manual)

As part of an alpha release, the current `master` content needs to be released as `latest` at the [docs repository](https://github.com/camunda/camunda-docs-manual).

#### 1. Tag the docs

```
git checkout master
git pull
git tag -a 7.9.0-alpha3 # the comment should be '7.9.0-alpha3'
git push origin 7.9.0-alpha3
```

#### 2. Release the tag as latest

Set the branch `latest` to the tag

```
git checkout latest
git reset --hard 7.9.0-alpha3
```

Edit the config file on the branch

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -1,5 +1,5 @@
 ---
-baseurl: "/manual/develop/"
+baseurl: "/manual/latest/"
 languageCode: "en-us"
 title: "Camunda BPM documentation"
 theme: "camunda"
@@ -20,7 +20,7 @@ params:
       url: "/enterprise"
   section:
     id: "manual"
-    version: "develop"
+    version: "latest"
     versionAlias: "7.5"
     versions:
       - "latest"
```

#### 3. Run `hugo` and check result

#### 4. Commit changes
```
git commit -a -m "chore(release): release master as latest"
```

#### 5. Force push to latest
```
git push --force-with-lease origin latest --tags
```

#### 6. Release Stage

* The content will be published to http://stage.docs.camunda.org/manual/latest/

* Make sure, that [this](https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-manual-stage%20(latest,%20latest)/) build has already been triggered and successfully passed through.

* Once the release is staged there, release it by triggering the following build:
[camunda docs release](https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(manual-latest)/)

* Now the new content is available in http://docs.camunda.org/manual/latest/

#### 7. Update redirects

**Only do this for alpha1 (ie. the first alpha after a minor release)**

In `camunda-docs-static`, edit the redirects

```diff
--- a/config/live/.htaccess
+++ b/config/live/.htaccess
@@ -12,7 +12,7 @@ RewriteRule ^latest/api-references/java/ /manual/7.3/reference/javadoc/

 # Javadoc
 RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12-SNAPSHOT [R=307,L]
-RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11 [R=307,L]
+RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12 [R=307,L]
 RewriteRule ^manual/([^/]+)/reference/javadoc/$ /javadoc/camunda-bpm-platform/$1 [R=307,L]

 # Manual without version
diff --git a/config/stage/.htaccess b/config/stage/.htaccess
index 972f0b6..e35ffde 100644
--- a/config/stage/.htaccess
+++ b/config/stage/.htaccess
@@ -7,7 +7,7 @@ RewriteRule ^latest/api-references/java/ /manual/7.3/reference/javadoc/

 # Javadoc
 RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12-SNAPSHOT [R=307,L]
-RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11 [R=307,L]
+RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12 [R=307,L]
 RewriteRule ^manual/([^/]+)/reference/javadoc/$ /javadoc/camunda-bpm-platform/$1 [R=307,L]

 # Manual without version
```

Commit and push

```bash
git add -A
git commit -m "release 7.12.0-alpha1"
git push origin master
```

# Release Javadocs

The javadocs are staged automatically by the release build at ``http://stage.docs.camunda.org/javadoc/camunda-bpm-platform/<minor-version>``

Verify that the correct javadocs are present.

After that, release the javadocs by triggering the following build:

``https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(javadoc-camunda-bpm-platform-<minor-version>)/``

Now the new javadocs are available here: ``https://docs.camunda.org/javadoc/camunda-bpm-platform/<minor-version>``

Make sure that you have set the right minor version within all the links. (e.g. 7.9, 7.12)

adjust the version in the URL, e.g. 7.9 or 7.12

# Release JIRA
The goal for releasing the JIRA is to add the version you would like to release to issues which have already been closed. To achieve this, please make sure that the alpha version you would like to release [already exists](https://app.camunda.com/jira/browse/CAM/?selectedTab=com.atlassian.jira.jira-projects-plugin:versions-panel). If not, ask Michael, Roman or Thorben to create this alpha version in JIRA.

To add the release version to the respective "fixVersion" field of all issues which have already been closed, go to the [issues page](https://app.camunda.com/jira/issues) and make sure that the advanced search is enabled.

Put the following query in the search box:

```
project = CAM AND fixVersion = <minor-version> AND fixVersion != <release-version> AND status = Closed AND fixVersion NOT IN(<minor-version>-alpha1, <minor-version>-alpha2)
```
Make sure that you have set the right minor version within the query. (e.g. 7.9.0, 7.12.0)

1. Click on "Tools -> all .. issue(s)".
1. Select all issues and click on "Next".
1. Select "Edit Issues" and click on "Next".
1. Select "Change Fix Version/s" and make sure that "Add to existing" is selected in the dropdown.
1. Select the version you would like to release.
1. Make sure that "Send mail for this update" is unchecked (bottom of the page).
1. Click on "Next".
1. Click on "Confirm".
1. After the completion of the bulk operation, click on "Ok, got it".

Put the following query in the search box of the [issues page](https://app.camunda.com/jira/issues):
```
project = CAM AND fixVersion = <release-version>
```
Perform a sanity check of the released issues by making sure that the `Fix Version/s` only includes the 
* current alpha version (e.g. 7.13.0-alpha2)
* the current minor version (e.g. 7.13.0)
* backport versions (optional, e.g. 7.12.3, 7.11.9)

If you have any questions feel free to approach Thorben. If Thorben is not available, you can ask Michael and/or Roman.

# Forward Security Reports

Determine all security reports for which fixes have are released and forward them to 1) Thorben 2) Roman. They will then take care of publishing security notices. Find all such issues with the following JIRA query:

```
project = CAM AND fixVersion = <release-version> AND type = "Security Report"
```

# Update the Community Download Page

You need to provide the release notes for the current version. You can find them in Jira:

https://app.camunda.com/jira/secure/BrowseProject.jspa/?selectedTab=com.atlassian.jira.jira-projects-plugin:versions-panel

Select the current version and hit the 'release notes' tab and provide the coresponding link.

You can try first to push your changes to [master](https://github.com/camunda/camunda.com-new/blob/master/data/releases.json) and check your change on [stage](https://app.camunda.com/jenkins/view/All/job/stage.camunda.com-new%20(master)/).

Note: "showAlpha": true when releasing alpha

Or you can push directly on live (keep in mind that everyone can see it after that). You need to update the following file:
https://github.com/camunda/camunda.com-new/blob/live/data/releases.json
(Similar to this [commit](https://github.com/camunda/camunda.com-new/blob/2517b2202b8ff9d5d33f5ef45c7ccc8e567a0ec8/data/releases.json#L18-L26))

Your push will trigger automatically a Jenkins job here:
https://app.camunda.com/jenkins/view/All/job/camunda.com-new%20(live)/
If everything is ok with it, you check your change under downloads page.

In case you don't have permissions for this repository please ask INFRA for help.

# Check Docker Images

Verify that the docker images [CE](https://hub.docker.com/r/camunda/camunda-bpm-platform/) and [EE](https://docs.camunda.org/manual/latest/installation/docker/#enterprise-edition) are built.

* CE job successfully run - ``https://ci.cambpm.camunda.cloud/job/<minor-version>/job/<minor-version>-platform-docker-ce/``
* EE job successfully run - ``https://ci.cambpm.camunda.cloud/job/<minor-version>/job/<minor-version>-platform-docker-ee/``

Make sure that you have set the right minor version within the link. (e.g. 7.9, 7.12)

# Release the Blogpost


#### 1. Update the release notes link

Update the link to the release notes in the blogpost's introduction. To generate it (if not already done in a previous step), go to [this page](https://app.camunda.com/jira/secure/ReleaseNote.jspa), select the version you would like to release, set the format to "html" and click on "Create". Ask Thorben if you do not have permissions to perform this step.

#### 2. Ensure that everyone has contributed their content.

#### 3. Proofread the paragraphs and check for potential enterprise features.

All enterprise features need to have a disclaimer like
```
> Please bear in mind that this feature is only available in the Enterprise Edition of the Camunda BPM platform. To try it out anyway, please request a [Free Trial or Quote](https://camunda.com/enterprise/)
```

#### 4. Squash all the commits to one single commit

```
git rebase -i master
```

Replace all 'pick' words with 's' except the first one, e.g.:

```
pick fda59df commit 1
s x536897 commit 2
s c01a668 commit 3
```

#### 5. Before you publish it ensure that the camunda.com download page is up to date.

Note: The title of the blogpost must be in the following format: `Camunda BPM <release-version> Released` (e.g. `Camunda BPM 7.9.0-alpha3 Released`)

#### 6. Publish the blog post by merging the branch with the master:

```
git checkout master
git merge 7.9.0-alpha3
```

#### 7. Check if everything worked out just fine at the [blog page](https://blog.camunda.org/).

# Publish the Enterprise Page

Check for unpublished commits (of other teams, e.g. Optimize) in [docs-static](https://github.com/camunda/camunda-docs-static/tree/master/enterprise), if there are some, sync with them if it is ok to publish the page.
Release the Enterprise documentation by triggering the following build:
https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(enterprise)/

# Send Release Notice

Let everyone know that a new release is available by sending the following email to alle@camunda.com with CC to marketing@camunda.com and team-support@camunda.com:

```
Hi everyone,
 
we have released Camunda BPM <release-version> today. Find all the details below: 

Blog Post: <link-to-blog-post>
Release Presentation: <presentation-date, e.g. Feb 28, 2020 02:00 PM (Berlin time)>, Join the presentation on Zoom: <link-to-zoom-call>
Release Notes: <link-to-release-notes> 
Download: https://docs.camunda.org/enterprise/download/

@marketing Please share this release on social media
@Team-Support Please include this release in the next customer notification

Best,
The Runtime Team
```

Concerning the Support Team:

You will receive a test email that needs to be approved.
⚠️ If you receive an Out of Office notification, you can ask Michael from QA to send the email.

Concerning the Marketing Team:

⚠️ If you receive no answer or an Out of Office notification, you can directly approach Gottfried from Marketing.

# Forum

Announce the release in https://forum.camunda.org by posting in the Announcements category linking to the blogpost (adapt elements in bold):

```
Title: Camunda BPM <release-version> released

Category: Announcements

Text:

Hi all,

Camunda BPM <release-version> has been released today.
Read all about it on our blog: <link-to-blog-post>.
```

# Improve this Guide

If you noticed any errors in the guides, any steps that were left out, please edit this document and keep it up to date. For changes to the release procedure create a ticket in the [CAMTEAM](https://app.camunda.com/jira/browse/CAMTEAM) project and follow the PR base workflow.

Note: For the version numbers used here it is ok to leave older versions in.

# Present and Celebrate the Release

In the afternoon, present the release remotely via Zoom. If you like you can moderate the presentation. If you prefer someone else do it (which is perfectly fine), please ask Thorben. All presenters should be present and be able to actively participate in a Zoom conference.

## Requirements

* post the blog post till then
* talk **before** with the people who should present the new features
* ask those people to set up demos on their machines that they can share on screen

# Pick Next Release Manager

The Release Manager Rotation is Round-Robin based on the [Community Worker Rotation](https://app.camunda.com/confluence/display/camBPM/Community+Worker+Process#CommunityWorkerProcess-CommunityWorkerRotation:).

After the Release Manager is picked, update the `CamBPM Release Manager` user group in Slack. To find it, go to:
1. `People` (in the left side-panel)
2. `User groups` tab
3. Locate and click on `CamBPM Release Manager`
4. Click on `Edit Members`
5. Remove the current Release Manager and add the new one.