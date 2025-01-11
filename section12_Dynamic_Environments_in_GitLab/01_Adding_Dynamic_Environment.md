### Dynamic Environments in GitLab CI/CD Pipelines

In this lecture, we realign the project's workflow to introduce **dynamic environments** for feature branches, moving away from the static environment setup used previously. This shift addresses the inefficiencies of having all developers deploy their features to a single, predefined staging environment.

#### Current Workflow and its Limitations

Currently, each developer deploys and reviews their feature directly in a single, static staging environment. This approach has the following drawbacks:
- **Concurrency Issues**: Multiple features in the same environment can overwrite or conflict with each other.
- **Testing Limitations**: Testing changes in isolation is difficult.
- **Error Propagation**: Bugs in one feature can affect other features being reviewed in the same environment.

#### Proposed Workflow: Dynamic Environments

The new workflow introduces **dynamic environments**:
1. Each feature branch gets its **own isolated environment** automatically created by the CI/CD pipeline.
2. These environments allow for independent testing and review without interference.
3. Once a feature is approved, its changes are merged into the `master` branch, triggering deployment to the **staging environment**.
4. After successful testing in staging, the changes are deployed to the **production environment**.

#### Automation for Review Apps

Previously, staging and production environments were manually set up using separate Heroku projects. However, with the new workflow:
- Environments for **review apps** (feature branches) will be created **dynamically** and **automatically** by the pipeline.
- The process includes creating a Heroku project, uploading the Docker image, releasing it, and setting it up for review.

#### Implementation Steps

1. **Branch Naming Conventions**:
   - Feature branches should start with a specific keyword, such as `feature-` or `review-`, e.g., `feature-test`.
   - While not mandatory, this naming convention is recommended. It:
     - Standardizes the naming structure across projects.
     - Allows protecting multiple branches using regex patterns (e.g., `feature-*`).

2. **Pipeline Changes**:
   - A new job, `deploy-feature`, is added to handle the deployment of dynamic environments.
   - This job runs after the Docker image for the feature branch is built.

3. **Deploy-Feature Job Configuration**:
   - Copy the structure of the `deploy-staging` job to save time.
   - Adjust the following values:
     - **Stage Name**: Change to `deploy-feature`.
     - **Environment**: Configure dynamically based on the branch.

4. **Script Steps**:
   - Pull the Docker image from the GitLab Container Registry.
   - Tag the image following **Heroku naming conventions**.
   - Define a variable for the dynamic app name:
     - Use the predefined GitLab environment variable `CI_ENVIRONMENT_SLUG`, which outputs a simplified, URL-safe version of the environment name.

#### Issues with GitLab Variables

There is an **open issue** in GitLab when one variable references another that contains an environment variable. For example:
- Variable `v1` is defined as: `$CI_JOB_NAME` (the job name).
- Variable `v2` is defined as: `The job name is $v1`.

**Expected Output**: `The job name is <actual job name>`.  
**Actual Output**: `The job name is $CI_JOB_NAME`.

This problem arises because GitLab cannot resolve variables nested within other variables. This limitation affects our pipeline, specifically:
- The `Heroku feature` variable depends on `feature app`, which references `$CI_ENVIRONMENT_SLUG`.
- This unresolved variable results in an invalid Heroku project name, causing the pipeline to fail.

#### Workaround for Variable Resolution Issue

While GitLab has yet to resolve this issue, a **workaround** exists:
- Instead of directly referencing variables, you can process and assign values dynamically using inline scripts or predefined shell commands within the pipeline.
- For instance, you can use a script to resolve `$CI_ENVIRONMENT_SLUG` at runtime and assign it directly to `Heroku feature`.

This workaround ensures that dynamic environments are created with valid names and URLs.

#### Next Steps

In the next lecture, we will:
1. Explore the workaround in detail.
2. Update the pipeline configuration to implement the workaround.
3. Test the new workflow to verify the creation and deployment of dynamic environments for feature branches.

#### Additional Notes

- Refer to the **resources tab** for the official GitLab thread discussing the unresolved variable issue.
- Dynamic environments are critical for ensuring scalability, isolation, and efficiency in CI/CD workflows, especially for larger teams and projects with multiple active feature branches.

--- 

This version provides additional details about the challenges, technical steps, and solutions to ensure a comprehensive understanding of the process.