## Deploy using IntelliJ IDE

1. Open the project in IntelliJ.
2. Click on 'Gradle' on the right hand side of the screen.
3. Select 'Tasks' -> 'application' -> 'bootRun'
4. A 'Build' folder will be generated.
5. Start the app and see the results in the web browser.


6. Sign in to AWS and go to ElasticBeanstalk
  - Create a new application
    - Choose a name.
  - Create a new Environment
    - 'Web Server Environment,
    - name = production
    - Platform = 'Managed'
    - Select 'Java'
    - Select 'use sample app'
  - Press 'Next'
    - Assign Service Role (create and assign using IAM)
    - Assign Instance Profile (create and assign using IAM)
  - 'Skip to Review' -> 'Submit'

7. When the Environment is launched we can use the 'Domain' to check the deployment was successful.

![App Deploy](<../Screenshots/Screenshot 2024-01-22 104145.png>)      

8. Go back to the Environment and choose 'upload and deploy'
  
![Alt text](<../Screenshots/Screenshot 2024-01-22 104345.png>)

  - Select the cars-api.jar file from build/libs
  - Select 'Deploy'

![Alt text](<../Screenshots/Screenshot 2024-01-22 104733.png>)

  - Wait for the Environment to be updated view the new application in the browser:


![Alt text](<../Screenshots/Screenshot 2024-01-22 105259.png>)