# Java Spring-API Deployment

## Deployment Using AWS ElasticBeanstalk


1. Create a new s3 bucket
2. Create a new variable on gitlab for the S3_BUCKET
3. Create a new User called 'gitlabci' under IAM.
  - attach elasticbeanstalk full access policy to user
  - generate access keys.
  - store access keys as variables in gitlab 
5. Create a .gitlab-ci.yml file in the root of the project.
6. Define stages, build job and a test job:
  - This builds the project, defines the artifacts and tests the status of the app. 

```
image: openjdk:12-alpine

stages:
  - build
  - test

build:
  stage: build
  script:
    - ./gradlew build
    - mv ./build/libs/cars-api.jar ./build/libs/$ARTIFACT_NAME
  artifacts:
    paths:
      - ./build/libs

smoke test:
   stage: test
   before_script:
     - apk --no-cache add curl
   script:
     - java -jar ./build/libs/cars-api.jar &
     - sleep 30
     - curl http://localhost:5000/actuator/health | grep "UP"

```

7. Create a deploy job setting the correct variables:

```
deploy:
  stage: deploy
  image:
    name: amazon/aws-cli
    entrypoint: [""]
  script:
    - echo "$AWS_DEFAULT_REGION"
    - aws configure set region $AWS_DEFAULT_REGION
    - aws s3 cp ./build/libs/cars-api.jar s3://$S3_BUCKET/cars-api.jar

```

8. we need gitlab to create the application and environment
   - we add the following lines to the deploy job:

```
    - aws elasticbeanstalk create-application-version --application-name $APP_NAME --version-label $CI_PIPELINE_IID --source-bundle S3Bucket=$S3_BUCKET,S3Key=$ARTIFACT_NAME
    - aws elasticbeanstalk update-environment --application-name $APP_NAME --environment-name "production" --version-label $CI_PIPELINE_IID

```
   - Gitlab will now update the version so we need to add this line to the build job so it updates on every build:

```
- mv ./build/libs/cars-api.jar ./build/libs/$ARTIFACT_NAME

```

9. Now is a good time for some cleanup:
    - define variables and replace in the script:
    - full script so far:

```
image: openjdk:12-alpine
variables:
  ARTIFACT_NAME: cars-api-V$CI_PIPELINE_IID.jar
  APP_NAME: cars-api

stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - ./gradlew build
    - mv ./build/libs/cars-api.jar ./build/libs/$ARTIFACT_NAME
  artifacts:
    paths:
      - ./build/libs

smoke test:
   stage: test
   before_script:
     - apk --no-cache add curl
   script:
     - java -jar ./build/libs/$ARTIFACT_NAME &
     - sleep 30
     - curl http://localhost:5000/actuator/health | grep "UP"

deploy:
  stage: deploy
  image:
    name: amazon/aws-cli
    entrypoint: [""]
  script:
    - echo "$AWS_DEFAULT_REGION"
    - aws configure set region $AWS_DEFAULT_REGION
    - aws s3 cp ./build/libs/$ARTIFACT_NAME s3://$S3_BUCKET/$ARTIFACT_NAME
    - aws elasticbeanstalk create-application-version --application-name $APP_NAME --version-label $CI_PIPELINE_IID --source-bundle S3Bucket=$S3_BUCKET,S3Key=$ARTIFACT_NAME
    - aws elasticbeanstalk update-environment --application-name $APP_NAME --environment-name "production" --version-label $CI_PIPELINE_IID



```
