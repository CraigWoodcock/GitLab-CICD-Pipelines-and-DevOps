# Creating a Static Website with Gatsby and Gitlab CI Manually

## Creating the Gatsby Website

1. Ensure Node.js and npm are installed.
  - Install node.js from website
  - Open terminal and check installation
    - `node --version`
    - `npm --version`
  - NPM may still be outdated, so lets update it.
    - `npm install -g npm` 
  
2. Install Gatsby CLI.
  - `npm install -g gatsby-cli`
- Check it is installed correctly.
  - `gatsby --version`  

3. Create a New Site.
  - `gatsby new <website name>`

4. Now we can cd into the directory and start developing.
  - `cd path/to/website/<website name>` 
  - `gatsby develop` - this creates the website for us.

5. Wait for confirmation that the website is ready to be viewed:

![confirmation](<Screenshots/Screenshot 2024-01-16 153206.png>)

- Now we can go to `http://localhost:8000/` to see the website

![Gatsby site](<Screenshots/Screenshot 2024-01-16 153602.png>)

## Create the Project on Gitlab

1. Go to GitLab Select 'New Project' and 'Blank Project'.
  - Give it a name
  - Select user in 'project url'
  - uncheck 'initialize with a README' as we will be pushing to this repo later.

2. Open bash terminal and add a remote origin.
  - cd into the directory
    - `git remote add origin git@gitlab.com:CraigWoodcock/my-static-website.git`
  - Check the remote version
    - `git remote -v`  

3. Now we can push our website to gitlab
  - `git push -u origin main`
  - head to gitlab and refresh the repo to see the files

## Build the Gatsby Project
1. we need to build the project
  - `gatsby build`
  - This will produce static HTML and JavaScript file
  - Compress CSS/JavaScript files to reduce download size
  - It will create a folder called 'public' that we can deploy
  - It will stop the localhost:8000 connection

2. CD into public folder to see contents
  - `cd public` - this is our 'artifact'
  - `ls`
  


