# Creating a GitLab Project

## Car Assembly Line Example
We are going to create a car assembly line. we are using car parts as an example to highlight that in order for the pipeline to secceed, each job relies on the previous job to be successfull. With a car, the chassis needs to be made first, then the engine can be mounted and then the wheels go on last because the drive-shaft needs to connect the wheels to the engine. If the wheels are in place before the engine, then we cannot install the drive-shaft and so the build process would fail.

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
   - yaml means "yaml ain't markdown language".

4. Now we need to add some commands to the file.
    - The following will create a job to add "Chassis", "engine" and "wheels" to a text file.
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