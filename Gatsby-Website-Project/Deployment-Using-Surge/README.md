# Deploying the Website Using Surge

## what is surge
surge is a cloud platform for serverless deployments
its easy to configure and use
simple deployment process
ideal for use with simple static websites


## Steps for Manual Deployment

1. we need to ensure node.js is installed and install surge via npm
  - From inside the /public folder:
  - `npm install --global surge`. 
  - the `--global` flag means it will be available for all users on the local machine

2. Run surge and create an account:
   - `surge`
   - Enter 'email address'
   - Enter 'Password'
   - Surge will detect the public folder so just press enter
   - It will now generate a domain name, we can change this now or later on.
   - wait for confirmation that surge has published the website.
 
![Alt text](<Screenshots/Screenshot 2024-01-17 123326.png>)

- Now we can view the website in the browser.

![Alt text](<Screenshots/Screenshot 2024-01-17 123335.png>)

