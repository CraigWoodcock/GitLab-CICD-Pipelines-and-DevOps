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
  - This will fail because the docker ruby image doesn't have node.js installed.
  - we need to specify a different image:
  - `image: node:lts` - use lts version of node image for this job.
  - We also need to add artifacts to the script so that we have an end product that we can deploy.

```
build the website:
  image: node:lts
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
  artifacts:
    paths:
      - ./public
```

4. Now we add the 'Test' stage:
  - We are going to use the `grep` command to search for a string, we can use the `-q` flag to stop the string from being returned
  - `echo "exit code was $?"` - this will return the exit code so we can see if the grep command worked.
  - We are going to add a test stage to test the artifact.
  - Here we have 1 passing and 1 failing test to make sure our tests work properly.

```
test artifact:
  stage: test
  script:
    - grep "Gatsby" ./public/index.html
    - grep "Gatsbybbbb" ./public/index.html
```
 
  - We have also added stages to the script:
```
stages:
  - build
  - test

build the website:
  image: node:lts
  stage: build
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
  artifacts:
    paths:
      - ./public


test artifact:
  stage: test
  script:
    - grep "Gatsby" ./public/index.html
    - grep "Gatsbybbbb" ./public/index.html
```

5. Now that we know our script works, we can perform some cleanup tasks.
  - Add the 'alpine' image, this is very small and will make the test run much quicker.
  - Remove the failing `grep` command.
  - Add a `-q` flag to the passing test to suppress the output.
  - Add a smaller image to the test job.

```
test artifact:
  image: alpine
  stage: test
  script:
    - grep -q "Gatsby" ./public/index.html
```

1. Add another test to test the website:
  - This is also using the node image
  - It sets up a server
  - Downloads the contents of the server and pipes it to the grep command 

```
test website:
  image: node:lts
  stage: test
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby serve
    - curl "http://localhost:9000" | grep -q "Gatsby"  
```
  - This will hang because the `gatsby serve` command will lock up the terminal while the server is running.
  - we need to add `&` to the end of the command - `gatsby serve &`
  - We can also add a sleep command to allow the server time to start before running the grep command
```
test website:
  image: node:lts
  stage: test
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby serve &
    - sleep 3
    - curl "http://localhost:9000" | grep -q "Gatsby"  
```
  - If the grep command fails we might need to use `| tac | tac |`
  - `curl "http://localhost:9000" | tac | tac | grep -q "Gatsby"`, this is because the grep command might try to close the read pipe before it has finished writing the whole page. `tac` will read the whole page but reverse its order so we run tac twice.