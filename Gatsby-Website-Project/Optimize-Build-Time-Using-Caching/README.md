# Optimizing Build Time Using Caching

1. Create a Global cache
  - key = branch name
  - paths = what to save (node modules(npm))
  - policy = what to do i.e pull/push (download/upload)
  - This will run before any other job and will pull a fresh version of the cache
  
```
cache: 
  key: $CI_COMMIT_REF_SLUG
  paths:
      - node_modules/
  policy: pull
```

2. Create an update cache job
  - Add cache to stages
  - We need to install npm in the cache so it is available for all jobs
  - Copy the cache, paste it into the job -> change it's policy to 'push'(upload)
  - make it a scheduled job
  - we also need to add `cache: {}` to all jobs that do not need npm

```
update cache:
  stage: cache
  script:
    - npm install
  cache: 
    key: $CI_COMMIT_REF_SLUG
    paths:
      - node_modules/
    policy: push
  only: 
    - schedules #only run when scheduled

```
3. Now make all of the other jobs non-scheduled so that they will run with the pipeline:
  - Add the following snippet:

```
  except:
    - schedules

```
- e.g:

```
test artifact:
  image: alpine
  stage: test
  cache: {}
  script:
    - grep -q "Gatsby" ./public/index.html
  except:
    - schedules
```

