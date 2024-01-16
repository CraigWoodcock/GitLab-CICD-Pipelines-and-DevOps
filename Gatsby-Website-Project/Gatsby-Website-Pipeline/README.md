# Creating a Static Website with Gatsby and Gitlab CI

1. we need to open the project in vscode
2. create a .gitlab-ci.yml file in the root directory
3. define steps to build the project
```
build the website:
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
```
- This will fail because the docker ruby image doesn't have npm installed.
- we need to specify a different image:
- `image: node:lts` - use lts version of node image
- We also need to add artifacts to the script so that we have and product.

```
build the website:
  image: node:lts
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
  artifacts:
    paths:
      - public
```

