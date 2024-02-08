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
