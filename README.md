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
