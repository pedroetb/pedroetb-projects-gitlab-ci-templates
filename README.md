# gitlab-ci-templates

Collection of CI/CD templates, inspired by <https://gitlab.com/redmic-project/gitlab-ci-templates>

[TOC]

## Templates description

You can use templates available at this project into your own projects, importing them at your `.gitlab-ci.yml` file.

Each template contains one or more *GitLab CI Jobs*, which you can use *as-is* or customize, overriding values or extending your own jobs from them.

Note that some templates have a base definition version, prefixed with an underscore (`_`). You can import them if this definition is more convenient to your project.

## Stages

*GitLab CI Jobs* from these templates run at a specific **stage** of your project *GitLab CI Pipelines*. Check description of each template for more info.

All jobs contained at these templates are prepared to run at any of [GitLab CI default pipeline stages](https://docs.gitlab.com/ci/yaml/#stages):

1. `.pre`
1. `build`
1. `test`
1. `deploy`
1. `.post`

If your project needs different stages or stage reordering, you may define these stages (honoring default stages, ordered as you need) at your project's `.gitlab-ci.yml`, or jobs at your custom stages will not run. Jobs stage value can be overwritten too.

## Templates usage

Templates are included into `.gitlab-ci.yml` like this:

```yaml
include:
  - project: 'pedroetb-projects/gitlab-ci-templates'
    ref: main
    file: '/test/auto.yml'

...
```

## Available templates

Templates are located at different directories, attending their stage and purpose.

### Build

Template files located into `build/` directory defines jobs which run at `build` stage.

#### Docker

Only for projects with Docker image definitions. Include Docker image building, tagging and pushing (and other container-related jobs).

* **build/docker/docker-build.yml**: Build a Docker image defined at your project, using [`pedroetb-projects/docker-build`](https://gitlab.com/pedroetb-projects/docker-build). Imports jobs from `build/docker/dockerfile-linting.yml` and `test/container-scanning.yml` too, so includes jobs at 2 different stages: `build` and `test`.
* **build/docker/dockerfile-linting.yml**: Run syntax checks over Dockerfile at your project. Already included at `build/docker/docker-build.yml`.

### Test

Template files located into `test/` directory defines jobs which run at `test` stage.

Perform some scanning jobs over project resources. All these jobs inherit from [GitLab CI templates](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates), adding specific configuration details.

* **test/auto.yml**: Include recommended scanning jobs.
* **test/code-quality.yml**: Identifies maintainability issues before they become technical debt.
* **test/sast.yml**: Static application security testing (SAST) discovers vulnerabilities in your source code before they reach production.
* **test/secret-detection.yml**: Minimize the risk of exposing your secrets.
* **test/dependency-scanning.yml**: Identify security vulnerabilities in your application's dependencies.
* **test/container-scanning.yml**: Run vulnerability checks over your Docker image. Already included at `build/docker/docker-build.yml`, but not by `test/auto.yml`.

### Deploy

Template files located into `deploy/` directory defines jobs which run at `deploy` stage.

#### Service deployment

Deploy a service (based in Docker) in a remote environment, using [`redmic-project/docker/docker-deploy`](https://gitlab.com/redmic-project/docker/docker-deploy).

* **deploy/service/[environment]-docker-deploy.yml**: Deploy one or more services defined at your project (using compose files and related contents) to a remote environment, which template file is defined at `deploy/service/`. Usage of custom Docker images is supported when they are pulled from your project's *GitLab Docker registry*.
