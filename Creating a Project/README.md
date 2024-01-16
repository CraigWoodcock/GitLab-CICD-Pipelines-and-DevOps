# Creating a GitLab Project

## Car Assembly Line Example
We are going to create a car assembly line. we are using car parts as an example to highlight that in order for the pipeline to succeed, each job relies on the previous job to be successful. With a car, the chassis needs to be made first, then the engine can be mounted and then the wheels go on last because the drive-shaft needs to connect the wheels to the engine. If the wheels are in place before the engine, then we cannot install the drive-shaft and so the build process would fail.

## Steps to Create the Pipeline

1. Sign up to GitLab, you can sign in with your github credentials.
2. Click on 'New project.
   - Choose 'Create Blank Project'.
   - Give the project a name
   - Choose the group in 'Project URL'
   - Set visibility of the project
   - Create Project

3. Now we need to add a yaml file
   - Choose 'New File' on the right hand side (this will open VSCode in the web browser).
   - Create a new file called '.gitlab-ci.yml'
   - yaml means "yaml ain't markdown language", it is a config file that defines steps to build, test and deploy applications.

4. Now we need to add some commands to the file.
    - The following will create a job called "build the car", Create a directory called "build", cd into the directory, Create a file called car.txt and add "Chassis", "engine" and "wheels" to the text file.
  ```
  build the car:
    script:
      - mkdir build
      - cd build
      - touch car.txt
      - echo "chassis" > car.txt
      - echo "engine" > car.txt
      - echo "wheels" > car.txt
  ```

5. On the Left Hand side, choose 'Source Control'
   - Enter a commit message and select 'Commit to 'main'.
   - This will save the file and GitLab will run the project automatically.

![Commit](<Screenshots/Screenshot 2024-01-16 110232.png>)<br>

6. We can press the green tick, (this will be a blue symbol if the pipeline is still running), to see the jobs 

![pipeline status](<Screenshots/Screenshot 2024-01-16 111305.png>)

7. Here we can see the jobs, if we select the job we can see the script that was executed and other information about the job.
   - Jobs screen.

![Jobs](<Screenshots/Screenshot 2024-01-16 111611.png>)<br>

   - Job info.

![Job Info](<Screenshots/Screenshot 2024-01-16 111621.png>)<br>

8. Now Lets add another job to the yaml file.
   - Go to the projects main page,
   - Select the yaml file and choose 'edit'
   - We can either use 'Pipeline Editor' or 'Web IDE'(Web IDE is VSCode).
   - Add the following to create a job that tests if each of the items in the previous job have been executed successfully.
   ```
   test the car:
     script:
       - test -f build/car.txt
       - cd build
       - grep "chassis" car.txt  
       - grep "engine" car.txt  
       - grep "wheels" car.txt
   ```

9. If we run the Pipeline now it will Fail. This is because GitLab will try to run both jobs in parallel and because the first job overwrites the car.txt file instead of appending to it. So when the build job has finished, there will only be "wheels" in the car.txt file. 
   - We need to add Stages so that GitLab will run them as individual stages
  

   ```
   stages:
     - build
     - test

   build the car:
     stage: build
     script:
       - mkdir build
       - cd build
       - touch car.txt
       - echo "chassis" > car.txt
       - echo "engine" > car.txt
       - echo "wheels" > car.txt

   test the car:
     stage: test
     script:
       - test -f build/car.txt
       - cd build
       - grep "chassis" car.txt  
       - grep "engine" car.txt  
       - grep "wheels" car.txt      
   ```   

   - This will still fail because we are still overwriting the file and because once job 1 is complete it destroys the files that it creates unless we tell it to save those items, so job 2 cannot find the objects that we are telling it to look for.
  
![Alt text](<Screenshots/Screenshot 2024-01-16 114611.png>)

![Alt text](<Screenshots/Screenshot 2024-01-16 115140.png>)

10. We need to define 'artifacts' to our 'build' stage.
   - This will save the 'build' folder that we created in job 1.
   ```
  artifacts:
    paths:
      - build/
   ```

   - Again, this will fail because we are still overwriting the contents of the car.txt and so the test will not find "chassis" or "engine"
   - We did this so that we could make sure the tests work correctly.

11. Now let's fix the issue.
   - we need to replace `>` with `>>` in the build stage for each item.
   - this will then append each line to the file and our tests will then pass.

```
stages:
  - build
  - test

build the car:
  stage: build
  script:
    - mkdir build
    - cd build
    - touch car.txt
    - echo "chassis" >> car.txt
    - echo "engine" >> car.txt
    - echo "wheels" >> car.txt
  artifacts:
    paths:
      - build/

test the car:
  stage: test
  script:
    - test -f build/car.txt
    - cd build
    - grep "chassis" car.txt  
    - grep "engine" car.txt  
    - grep "wheels" car.txt      

```

12. Now the Pipeline passes.
    - We can view/download the artifacts by selecting the 'download' button on the right.
  
![Pipeline](<Screenshots/Screenshot 2024-01-16 122133.png>)
