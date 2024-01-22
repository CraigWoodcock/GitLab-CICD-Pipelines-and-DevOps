# Branches and Merge Requests

specify branch to use for the job:
- add this command to deploy to staging, deploy to production and production tests

```
only:
  - main
```

- All jobs before this will only run before the merge to main and they will also run again on the main branch
  - Add this command to make sure they only run before the merge:
  
```
only:
  - main
  - merge_requests
```

make a new branch on gitlab
- Code -> Branches -> New Branch -> create from main
- the pipeline will run the first set of jobs.


## merge requests

settings -> repository -> protected branches
- Allowed to merge - 'Maintainers'
- Allowed to push - 'No one'

settings -> merge requests -> check the following:
- fast-forward merge
- enable 'delete source branch'
- encourage squashing
- pipeline must succeed

switch to new branch
- make a change
- push to new branch
- create merge request on gitlab
- press 'set to auto merge'

## dynamic environments

here i created an environment for the deploy review job:

this creates an environment where we can deploy our website so see a working model in a test environment so that we do not have to push to the main production code.

we added a deploy review job to create the environment
- this only runs with the merge request
- the idea is that we can see a working model while still on a feature branch
- with this step, a review could be carried out manually and then the environment stopped when review is complete

```
deploy review:
  stage: deploy review
  only:
    - merge_requests
  environment: 
    name: review/$CI_COMMIT_REF_NAME # create a new environment called the same as the branch name
    url: https://carniverouse1-$CI_ENVIRONMENT_SLUG.surge.sh
    on_stop: stop review  
  script:
    - npm install --global surge
    - surge --project ./public --domain $STAGING_DOMAIN
  except:
    - schedules    

```

we then added a stop review job to stop the review environment automatically
- this will stop the environment on gitlab and instruct surge to destroy the deployment.
- again, this job only runs with the merge request
```
stop review:
  stage: deploy review
  only:
    - merge_requests
  variables:
    GIT_STRATEGY: none
  script:
    - npm install --global surge
    - surge teardown carniverouse1-$CI_ENVIRONMENT_SLUG.surge.sh
  when: manual
  environment:
    name: review/$CI_COMMIT_REF_NAME
    action: stop
  except:
    - schedules  

```







