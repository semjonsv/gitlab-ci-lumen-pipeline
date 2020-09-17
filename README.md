# Gitlab Lumen Test Pipeline

### Simple CI pipeline that seeds and runs tests (with prebuilt image)

![pipeline](https://github.com/semjonsv/gitlab-ci-lumen-pipeline/blob/master/pipeline.png)

````yaml
stages:
  - build
  - test

# php image should contain pdo dependencies, composer, etc.
image: registry.gitlab.com/[your-registry]/[project]

variables:
  MYSQL_ROOT_PASSWORD: secret
  MYSQL_DATABASE: homestead

build:
  stage: build
  script:
    - echo "Build"
    - php -v
    - composer install --prefer-dist --no-ansi --no-interaction --no-progress --no-scripts
    - cp .env.example .env
  artifacts:
    paths:
      - vendor/
      - .env

unit-test:
  stage: test
  services:
    - mysql:8.0
  script:
    - echo "Unit Test"
    - php artisan migrate:fresh --seed
    - ./vendor/bin/phpunit --log-junit report.xml
  artifacts:
    reports:
      junit: report.xml
````
