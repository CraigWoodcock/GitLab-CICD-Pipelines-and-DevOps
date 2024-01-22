# Automating Deployment Using Surge and GitLab CI

## Generate Surge Token

1. from the public directory in bash:
  - type `surge token`
  - copy the token

2. Add Token to GitLab:  
  - go to gitlab repository
  - go to settings -> CICD -> variables -> add variable
  - key = `SURGE_LOGIN`
  - value = email address
  - uncheck 'protected variable'
  - add another variable
  - key = `SURGE_TOKEN`
  - uncheck 'protected variable'
  - check 'mask variable'
  
## Creating the Script

1. create a new job in the script:
   - install surge
   - define the project and domain to use 
      - this needs to be as unique as possible.
  
```
deploy to surge:
  script:
    - npm install --global surge
    - surge --project ./public --domain <makeAuniqueName>.surge.sh
```

2. Add deploy stage to the script
   - Remove node image from individual jobs and move to top of the script
   - add deploy stage to script and jobs
   - push changes and see the pipeline pass
  
```
  image: node:lts
stages:
  - build
  - test
  - deploy
build the website:
  stage: build
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
  artifacts:
    paths:
      - ./public


test artifact:
  image: alpine
  stage: test
  script:
    - grep -q "Gatsby" ./public/index.html
    - echo "===EXIT CODE $?==="


test website:
  stage: test
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby serve &
    - sleep 3
    - curl "http://localhost:9000" | tac | tac | grep -q "Gatsby"
    
deploy to surge:
  stage: deploy
  script:
    - npm install --global surge
    - surge --project ./public --domain carniverouse-vegintable.surge.sh
  ```

5. test the deployment:
  - Create a deployment test job
  - Add test stage to the job
  - image will be alpin
  - we need to use apk to install curl

```   
deployment test:
  stage: production tests
  image: alpine
  script:
    - apk add --no-cache curl
    - curl "carniverouse-vegintable.surge.sh" | tac | tac | grep -q "Welcome to"
```
we can make certain jobs manual only using the follwing command:
- this will stop the job and queue all other jobs until this job has been run

```
when: manual # only runs manually
  allow_failure: false # stop the pipeline here
```

