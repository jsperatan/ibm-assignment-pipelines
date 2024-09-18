# ibm-assignment-pipelines

## Components

- Docker
- Jenkins Server (http://54.254.172.152:8080/) - Address subjected to change as I am currently using the Free Tier AWS which may occassionally go OOM. Please contact me if URL is not accessible. Credentials are sent separately
- Docker-in-Docker (DinD)
- Docker Hub Account and Repository

## Pipeline Summary

1. Pull source code from Github repository
2. Build Docker image and tag it accordingly
3. Run the Docker image so that it can be used for additional testing
4. Sleep for 10 seconds just to make sure application is ready to accept connections
5. Test REST endpoints
6. Login to Docker Hub
7. Push Docker image to Docker Hub
8. Clean up - Stop and remove running container, remove images and logout from Docker Hub

## Setting Up

1. Launch AWS EC2 Instance for hosting Jenkins server
2. [Run Jenkins as Docker Container](https://www.jenkins.io/doc/book/installing/docker/)
3. Running DinD - Execute the following command from the Jenkins server host: `sudo docker run   --name jenkins-docker  --rm   --detach   --privileged   --network jenkins   --network-alias docker   --env DOCKER_TLS_CERTDIR=/certs   --volume jenkins-docker-certs:/certs/client   --volume jenkins-data:/var/jenkins_home  --expose 5656 --publish 2376:2376 --publish 5656:5656   docker:dind   --storage-driver overlay2`
   - I have added an additional step to test the REST endpoint before pushing the images to Docker Hub. Due to this, it is required to also expose port 5656 from the DinD container so that it can be reached from the Jenkin server container
4. Access Jenkins UI (http://54.254.172.152:8080/) and login using the given credentials

## Creating CI/CD Pipelines

1. From the Jenkins Dashboard, select **"New Item"** from the sidebar
2. Enter the item name and select **"Pipeline"**. Click **"OK"** to confirm
![image](https://github.com/user-attachments/assets/7f532d3f-49be-4e7a-a5ba-c782a4746914)
4. From the **"Configure"** page, select **"Advanced Project Options"** from the sidebar. Under the **"Pipeline"** tab, copy the content from the Jenkinsfile into the script box
![image](https://github.com/user-attachments/assets/3eabb040-97e7-4c74-a737-bc34bc6ac0ef)

## Adding Docker Hub Credentials

1. Login to your Docker Hub account
2. Select your profile and open **"Account Settings"**
3. From the **"Account Settings"** page, open **"Personal access tokens"** under the **"Security"** Tab
4. Enter a description for the access token and enable **"Read & Write"** permissions
5. Copy the displayed access token
6. Navigate to the **"Home"** page of the Jenkins server. Under the sidebar, select **"Manage Jenkins"**
7. Select **"Credentials"**. Under the **"Credentials"** tab, select **"System"**
![image](https://github.com/user-attachments/assets/cd324c0c-07c4-4c6a-98a6-281bdf1a1ea9)
9. Select **"Global credentials (unrestricted)"**
10. Enter your Docker Hub username into the **"Username"** field and paste the newly created access token into the **"Password"** field. Save the credential
![image](https://github.com/user-attachments/assets/49b015a0-d5ab-4cd1-9e84-c96ed7cddd1c)


## Running Pipelines

1. From the Jenkin Dashboard, select the newly created Pipeline
2. From the sidebar, select **"Build Now"**
3. Monitor build status from the **"Build History"** tab
![image](https://github.com/user-attachments/assets/3f0f7692-6733-42af-b7f9-83b93b43fd41)

