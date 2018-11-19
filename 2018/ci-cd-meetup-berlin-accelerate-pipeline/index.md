class: center, middle

# Gitlab CI

#### Engineering Workflows

![Logo](logo.png) 

.left[
David Rubin]
.left[
[<i class="fa fa-twitter" aria-hidden="true"> drubin87</i>](http://twitter.com/drubin87)]
.left[
[<i class="fa fa-github" aria-hidden="true"> drubin</i>](http://github.com/drubin)]
.left[
[<i class="fa fa-linkedin" aria-hidden="true"> David Rubin</i>](https://www.linkedin.com/in/drubin87)]
.left[
[<i class="fa fa-link" aria-hidden="true"> garden.io</i>](https://garden.io)]

---
class: center, middle
### Human beings, who are almost unique in having the ability to learn from the experience of others, are also remarkable for their apparent disinclination to do so.
  \-  Douglas Adams, Last Chance to See

---
class: center, middle

# CI is an attempt to overcome this.

---
class: center, middle

# Learning to ride a bike?

---
class: center, middle

![Logo](Brum_Brum_Wooden_Balance_Bike_for_Kids.jpg)

.footer[BrumBrumBikes [CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0)], from Wikimedia Commons]

---
class: center, middle

![Logo](768px-Helmeted_boy_on_training_wheels.jpg)

.footer[Dawn Endico from Menlo Park, California [CC BY-SA 2.0 (https://creativecommons.org/licenses/by-sa/2.0)], via Wikimedia Commons]

---
class: center, middle

![Logo](640px-Bicycle_race_scene,_1895.jpg)

.footer[Calvert Lithographic Co., Detroit, Michigan [Public domain], via Wikimedia Commons]

---
class: center, middle
### It’s all about the journey, not the outcome.
  \-  Carl Lewis

---
class: middle
## Engineer The Journey

* **First Impression**
  * Readme
  * High Level features and functions
  * Engineer for success 
* **Quickstart designed to “just work”**
  * Training wheels
  * Take the quickstart and make it your own
  * FAQ of potential failure’s
* **Path to becoming an expert**
  * Low level API docs 
  * Advanced features 
  * How to deal with exceptions
---
class: center, middle
### Treat Gitlab as a product not a tool

---
class:  middle
### Simple Node Application

```
tree
.
├── Dockerfile
├── index.js
├── package-lock.json
└── package.json

0 directories, 4 files```

index.js
```js
const express = require('express')
const app = express()
app.get('/', (req, res) => res.send('Hello World!'))
app.listen(3000)
```

DockerFile
```docker
FROM node:10.13-alpine
WORKDIR /app
ADD package.json package-lock.json /app/
RUN npm install --production
ADD . /app
CMD ["npm", "start"]

```
---
class: center, middle
## Now what? 

---
class: middle
## Release flow

.gitlab-ci.yml
```yml
# Opt-in stages
stages:
  - setup
  - test
  - release

# Include workflows 
include:
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/node.yml
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/docker.yml
  
# node version to run tests
image: node:10.13-alpine
```
![Logo](node-pr-pipeline.png) 
![Logo](node-master-pipeline.png)
---
class: middle
## ci-base
```
├── .gitlab-ci.yml
├── Dockerfile
├── README.md
├── bin
│   ├── docker-build
│   ├── docker-help
│   ├── docker-login
│   ├── docker-push
│   ├── workflow-master
│   └── workflow-tags
└── includes
    ├── docker.yml
    └── node.yml
```
.footer[https://gitlab.com/drubin/ci-base/]
---
## .center[ci-base/.gitlab-ci.yml]

```yaml
.references:
  # .... snipped
  test-script: &test-script
    stage: test
    <<: *docker-image
    <<: *use-docker
    before_script:
      - ./bin/docker-login
      - TEST_SCRIPT="${CI_JOB_NAME##* }" # Last part of job's name is the tests name
      - export PATH="$PWD/bin:$PATH" # Export bin/ into path since not running custom image
      - ./bin/$TEST_SCRIPT

# Integration tests
test docker-build:
  <<: *test-script
  script:
    # Verify image is built
    - docker inspect "$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA"

# Test workflows
build latest:
  stage: release
  <<: *docker-image
  <<: *use-docker
  script:
    # manually add scripts to path since we aren't using pre-built images
    - export PATH="$PWD/bin:$PATH"
    - workflow-master
  only:
    - master
```
.footer[https://gitlab.com/drubin/ci-base/blob/master/.gitlab-ci.yml]

---
## Changeset for bin/docker-build
```diff
docker pull "$CI_REGISTRY_IMAGE:latest" || true
- docker build --cache-from "$CI_REGISTRY_IMAGE:latest" --tag "$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA" .
+ docker build --cache-from "$CI_REGISTRY_IMAGE:latest" --tag "$CI_REGISTRY_IMAGE:broken" .
```

Pipeline Status

![ci-base](ci-base-broken-pr.png)
---
class: middle, center

# Mind blown 

Using gitlab-ci to test your gitlab-ci?

Unit testing your testing tools?

Can some one say recursion recursively?


---
class: middle
##  Node Workflow
```yaml
# Include workflows 
include:
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/node.yml
 ```

```yaml
.references-node:
  node-test: &node-test
    #...

install:
  stage: setup
  script:
    - npm install 
  cache:
    paths:
      - node_modules

lint:
  <<: *node-test
  script:
    - npm lint
  
test:
  <<: *node-test
  script:
    - npm test
```
![Logo](node-pr-pipeline.png) 
---
##  Docker Workflow
```yaml
# Include workflows 
include:
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/docker.yml
 ```
```yaml
# Workflows 
build latest:
  stage: release
  <<: *docker-image
  <<: *use-docker
  script:
    - workflow-master
  only:
    - master

build tags:
  stage: release
  <<: *docker-image
  <<: *use-docker
  script:
    - workflow-tags
  only:
    - tags
```
![Logo](node-master-pipeline.png)
---
class: middle
## Simple Node application

```
tree
.
├── .gitlab-ci.yml
├── Dockerfile
├── index.js
├── package-lock.json
└── package.json

0 directories, 5 files
```

.gitlab-ci.yml

```yaml
# Opt-in stages
stages:
  - setup
  - test
  - release

# Include workflows 
include:
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/node.yml
 - https://gitlab.com/drubin/ci-base/raw/v0.1/includes/docker.yml
  
# node version to run tests
image: node:10.13-alpine
```
![Logo](node-pr-pipeline.png) 
