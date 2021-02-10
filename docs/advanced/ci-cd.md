# CI/CD 

CI stands for Continuous Integration and CD for Continuous Development. It is an [Agile](https://www.agilealliance.org/agile101/the-agile-manifesto/) concept closely related to Test Driven Development (TDD)

The idea is to run a tought ahead set of tests whenever we apply a change during development, in doing so we have a feedback loop as small as possible that allows us to quickly identify eventual new problems

A real-world situation would be, for example, using a versioning platform (like GitHub) with a pipeline service (like Travis CI) that automatically runs our test units in any needed environment, whenever we make a new commit

We'll immediately see if something goes wrong, and easily locate where the problem comes from depending of the quality of our tests set



## Travis CI

[Travis CI](https://www.travis-ci.com/)

Configuration example (in YAML) for a **.travis.yml** file, running tests for a todo-list app running on eQual :

```yaml
dist: xenial
language: php
php: 
  - '7.2'  
  
services:
  - mysql

env:
  global:
    - DB_NAME="equal"
    
before_script:
  # install apache2
  - chmod +x .travis/*.sh
  - .travis/init-php.sh 
  
script: 
  # check that mandatory directories are present and have correct access rights set
  - php run.php --do=test_fs-consistency
  # check ability to connect to the dbms service
  - php run.php --do=test_db-connectivity
  # create an empty database 
  - php run.php --do=init_db  
  # initialise database & demo data
  - php run.php --do=init_package --package=core
  - php run.php --do=init_package --package=todolist
  # run test units
  - php run.php --do=test_package --package=todolist
```



## BitBucket Pipelines

[BitBucket](https://bitbucket.org/) has a built-in testing environment that relies on [Docker](https://www.docker.com/)

They have an amazing [tutorial to get started](https://support.atlassian.com/bitbucket-cloud/docs/get-started-with-bitbucket-pipelines/), aswell as a [YAML validator](https://bitbucket-pipelines.prod.public.atl-paas.net/validator) with all the information you need

Here is a minimal syntax example taken from their page about [build artifacts](https://support.atlassian.com/bitbucket-cloud/docs/publish-and-link-your-build-artifacts/) :

```yaml
image: python:3.5.1

pipelines:
 branches:
  master:
   - step:
    script:
     - python run_tests.py
```

