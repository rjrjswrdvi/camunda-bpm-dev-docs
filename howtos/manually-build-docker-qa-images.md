# Manually Build & Push the Docker QA Images

> Heads-Up: The recommended way of building the images is via [Jenkins Job] and not manually.

## Prerequisites

* Docker
* Git
* VPN Account & Client (optional, only required for step 9)\
Find all the details here: https://confluence.camunda.com/display/ADMIN/VPN

## Step-By-Step Guide

1. Run `docker login registry.camunda.cloud`
   * Enter your Camunda OKTA credentials if not logged in already
2. Clone https://github.com/camunda/camunda-bpm-platform-ee
3. Check out the tag of the release version without ee suffix (e. g. 7.13.0-alpha3)\
   **Heads-up:** Having `master` checked out leads to building the latest snapshot
4. Navigate to the folder `qa/docker` in the terminal
5. Run `mvn versions:update-parent -DparentVersion=<CAMUNDA VERSION WITH EE SUFFIX>`
   * Version example: `7.13.0-alpha3-ee`
   * The step is optional for the master snapshot
6. Run `mvn clean install`
   * If you prefer to do a dry-run first, you can use `-Ddocker.skip.push` to skip pushing the images to the Docker registry
7. When the command has been successfully completed, check if the images are available (login with your Camunda OKTA credentials):
     * https://registry.camunda.cloud/harbor/projects/6/repositories/team-cambpm%2Fweblogic12r1
     * https://registry.camunda.cloud/harbor/projects/6/repositories/team-cambpm%2Fweblogic12r2
     * https://registry.camunda.cloud/harbor/projects/6/repositories/team-cambpm%2Fwebsphere9
8. Promote the version of the Docker image in the [Portainer Templates Repository].
   * You can find an example commit here: https://github.com/camunda-ci/portainer-templates/commit/d94033
9. To confirm that everything works as expected, go to Portainer, run a container for each image, and check the "Stack Details". You should validate the right version is picked up, and the container is "runnable". Please see the following screenshot:\
   ![Portainer Stack Details]
   * The step requires a VPN connection

[Jenkins Job]: https://ci.cambpm.camunda.cloud/view/all/job/7.13/job/7.13-platform-docker-qa/
[Portainer Templates Repository]: https://github.com/camunda-ci/portainer-templates
[Portainer Stack Details]: https://raw.githubusercontent.com/camunda/camunda-bpm-dev-docs/master/howtos/img/manually-build-docker-qa-images-portainer-stack-details.png
