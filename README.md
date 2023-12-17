# gitlab-ci-templates

Collection of CI/CD templates, inspired by <https://gitlab.com/redmic-project/gitlab-ci-templates>

## Templates description

You can use templates available at this project into your own projects, importing them at your `.gitlab-ci.yml` file.

Each template contains one or more *GitLab CI Jobs*, which you can use *as-is* or customize, overriding values or extending your own jobs from them.

Note that some templates have a base definition version, prefixed with an underscore (`_`). You can import them if this definition is more convenient to your project.

## Stages

*GitLab CI Jobs* from these templates run at a specific **stage** of your project *GitLab CI Pipelines*. Check description of each template for more info.

You must reference these stages (ordered as you need) at your project's `.gitlab-ci.yml`, or jobs will not run. Jobs stage value can be overwritten too, is you wish.

Here is a list of all stages used by template jobs by default, with suggested order:

1. `test`
1. `pre-package`
1. `package`
1. `post-package`
1. `deploy`

## Templates usage

Templates are included into `.gitlab-ci.yml` like this:

```yaml
include:
  - project: 'pedroetb-projects/gitlab-ci-templates'
    ref: master
    file: '/packaging-docker/docker-build.yml'

stages:
  - pre-package
  - package
  - post-package

...
```

## Templates available

Templates are located at different directories, attending it purpose.

### Deployment service

Deploy a service (based in Docker) in a remote environment, using [`redmic-project/docker/docker-deploy`](https://gitlab.com/redmic-project/docker/docker-deploy).

* **custom-image.yml**: Adapt jobs from `deployment-service/_docker-deploy.yml` to use a custom Docker image, defined at your project and uploaded to *GitLab Docker registry* of your project.

* **docker-deploy.yml**: Deploy one or more services defined at your project (using compose files and related contents) to a remote environment, defined by the subdirectory of `deployment-service/` where template file is located. You can also provide your own environment definition, overriding default values or extending from `deployment-service/_docker-deploy.yml`. Run at `deploy` stage.

### Packaging Docker

Only for projects with Docker image definitions. Include Docker image building, tagging and pushing, and also Dockerfile linting and Docker image scanning.

* **docker-build.yml**: Build a Docker image defined at your project, using [`pedroetb-projects/docker-build`](https://gitlab.com/pedroetb-projects/docker-build). Imports jobs from `packaging-docker/dockerfile-linting.yml` and `scanning/container-scanning.yml` too. Include 3 stages: `pre-package`, `package` and `post-package`.
* **dockerfile-linting.yml**: Run syntax checks over Dockerfile at your project. Run at `pre-package` stage. Already included at `packaging-docker/docker-build.yml`.

### Scanning

* **container-scanning.yml**: Run vulnerability checks over your Docker image. Run at `post-package` stage. Already included at `packaging-docker/docker-build.yml`. Inherit from [GitLab CI templates](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates).
* **dependency-scanning.yml**: Lookup dependencies at your code, finding vulnerabilities and licenses compliance status. Run at `test` stage. Inherit from [GitLab CI templates](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates).
