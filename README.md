<h2>Project Architecture.</h2>

<br/>
<img src="https://i.imgur.com/W2picNj.png" height="80%" width="80%" alt="the project architecture"/>
<br />


The GitOps involves two Git repositories - one for Terraform code (infrastructure) and another for application code. Each repository has its own GitHub workflow to detect and apply changes.

1. Setting Up Repositories and Workflows:
   - Fork two Git repositories (Terraform and application).
   - Set up SSH authentication with Git repositories.
   - Use Visual Studio Code to write workflows.

2. Terraform Code Workflow:
   - Utilizes GitHub Actions to create a workflow.
   - Detects changes in the staging branch.
   - Performs testing using `Terraform validate` and `Terraform plan` against AWS Cloud.
   - If validation is successful, the staging branch is merged into the main branch (which is locked).
   - A pull request is created, and changes are approved by the main branch owner.
   - Changes in the main branch trigger the workflow to apply the Terraform code at the infrastructure level (AWS, VPC, and EKS).

3. Application Code Workflow:
   - In a separate repository, focuses on application code, Dockerfile, and Kubernetes definition files.
   - Workflow fetches code, builds, tests, and deploys it.
   - Uses Maven with check style and Sonar CLI for code analysis.
   - Builds Docker images and uploads them to Amazon ECR.
   - Utilizes Helm Charts for bundling Kubernetes definition files, including a variable for image tag information.
   - Executes Helm Charts on an EKS cluster, where Kubernetes detects changes in the image tag, fetches the image from ECR, and runs the application.

<h2>NOTE:</h2> The project architecture involves two repositories and two workflows. One workflow applies infrastructure changes (Terraform), and the other applies application changes (Maven, Docker, Helm Charts).


<h2>Steps for Setup:</h2>
Fork the 2 repositories with the source code, Do the SSH key exchange from the computer to the github account and clone the repositories
<br/>
<img src="https://i.imgur.com/kR7zPpo.png" height="80%" width="80%" alt="clone the repositories"/>
<br />
Making the code changes in the stage branch, If it works perfectly then it will be merged with the main branch
<br/>
<img src="https://i.imgur.com/rsBMgge.png" height="80%" width="80%" alt="staging"/>
<br />

<h2>Creating Github Secrets:</h2>

Opened the Secrets section for the 2 GitHub repositories.
Created an IAM User
Created an S3 Bucket with a unique name, Copy the bucket name, Store the bucket name in GitHub Secrets of the repository with the name BUCKET_TF_STATE.
Created an ECR Repository, Copy the repository URI, Store the repository URI in GitHub Secrets of the repository with the name REGISTRY.
GitHub Secrets Configuration: For each repository, add secrets with specific names, in each of the repositories, add the secret keys
These steps ensure that sensitive information such as access keys and resource identifiers are securely stored in GitHub Secrets, minimizing the risk of accidental exposure and potential security breaches.
<br/>
<img src="https://i.imgur.com/Ub9ziyv.png" height="80%" width="80%" alt="staging"/>
<br />
The terraform file.

<br/>
<img src="https://i.imgur.com/fyHL700.png" height="80%" width="80%" alt="terraform"/>
<br />
Vpf.tf
<br/>
<img src="https://i.imgur.com/5huRIrc.png" height="80%" width="80%" alt="vpf.tf"/>
<br />
<h2>Creating staging workflow for terraform:</h2>
Here i wrote workflow, workflow is the same thing as pipeline for jenkins. Its called workflow for github actions, instead of doing it manually, I wrote the code which will be pushed and the workflow will be automatically created.
<br/>
<img src="https://i.imgur.com/36P8tRD.png" height="80%" width="80%" alt="workflow"/>
<br />
The test flow was successful with the github actions after series of troubleshooting
<br/>
<img src="https://i.imgur.com/F3wZxrr.png" height="80%" width="80%" alt="workflow"/>
<br />
the steps that was executed
<br/>
<img src="https://i.imgur.com/xzWa5DR.png" height="80%" width="80%" alt="workflow"/>
<br />

<h2>I added steps to the workflow to apply Terraform changes only if there are changes on the main branch.</h2>
Terraform Apply step is added to the workflow, configured to run terraform apply -auto-approve only if there are changes on the main branch.
<br/>
<img src="https://i.imgur.com/72Qo8zp.png" height="80%" width="80%" alt="workflow"/>
<br />
Additional steps are added to install the Nginx Ingress Controller on the Kubernetes cluster for deploying the application.
Steps are conditionally executed based on the success of previous steps, ensuring that only successful Terraform applies trigger the installation of the Ingress Controller.
The process of merging changes from the staging branch to the main branch is explained using Git commands.
The concept of branch protection rules and pull requests is briefly mentioned for real-time collaboration and code review.
The completion of the workflow successfully applying the Terraform changes and setting up the EKS cluster is confirmed through Management Console

`The workflow ensures that changes to infrastructure are applied only after thorough testing on the staging branch and approval via pull request on the main branch, maintaining control and reliability in infrastructure management`

<h2>workflow for the application code</h2>
So the EKS cluster is created successfully, the Kubernetes cluster is up and running ready to be used.
Now, I will create another workflow, which will be in the second repository which is the application repository, there's a separate Git repository for application code, so the workflow is going to fetch the code, test the code by using Maven Checkstyle and Sonar analysis, then build the Docker image, upload it to Amazon ECR, then I will build the Helm charts, which when deployed is going to fetch the latest image from ECR and run the application.
`But first I will be going SonarCloud to create an organization, project and token, and store it on GitHub Secrets`

<br/>
<h2>Steps for that</h2>
<br />

1. Create SonarCloud Organization and Project: Log into SonarCloud and create a new organization, Manually create a new organization with a unique name, Create a new project within the organization
2. Generate Token for Authentication: Generate a token on SonarCloud for authentication purposes.
3. Store Secrets on GitHub:
    - Add repository secrets for:
    - SONAR_TOKEN: The token generated from SonarCloud.
    - SONAR_ORGANIZATION: The name of the SonarCloud organization.
    - SONAR_PROJECT_KEY: The key of the project created on SonarCloud.
4. Review Code and Configuration:Review the source code of the application, including Dockerfile and Kubernetes definitions. Ensure that necessary configurations like Docker image names, Kubernetes deployment files, and Ingress rules are in place.
5. Create Workflow File:Define the workflow including, and Trigger events.
6. Testing Job: Set up a job to test the code using Maven.Fetch the code, run Maven tests, and check style.
7. SonarCloud Analysis:
   - Set up a job to run the SonarScanner CLI for code analysis.
   - Specify Java version (Java 11) using `actions/setup-java`.
   - Use SonarScanner CLI to scan the code, passing necessary parameters such as URL, authentication token, organization name, and project key.
   - Upload analysis results to SonarCloud.
8. Quality Gate Check:
   - Integrate a quality gate check using an action from the GitHub Marketplace.
   - Specify authentication token and URL for SonarCloud.
   - Define conditions for passing the quality gate based on predefined rules (e.g., number of bugs).
9. Execution and Error Handling:
   - Run the workflow on GitHub Actions.
   - Handle errors encountered during the execution, such as SonarQube server unreachable or failing quality gate checks.
   - Address errors by updating configurations or creating new quality gates on SonarCloud.
10.Final Execution:
   - Rerun the workflow after addressing errors.
   - Ensure successful completion of the workflow, verifying logs and analysis results.

`By following these steps, the SonarCloud integration for code analysis is set up within the GitHub repository, allowing for automated testing, analysis, and quality gate checks`
<br/>
The second work flow
<br/>
<br/>
<img src="https://i.imgur.com/gPfJA0O.png" height="80%" width="80%" alt="2nd workflow"/>
<br />
