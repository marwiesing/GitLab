### Welcome to this lecture!

Before wrapping up this project, we have one last improvement to make to the project code to optimize its functionality.

---

### Current Scenario:

As of now, the workflow for adding a new feature to our application works as follows:
1. **Code Development**: When a new feature is to be added, the application developer writes code for that feature in a dedicated feature branch.
2. **Dynamic Environment Creation**: During deployment, the application is deployed to a temporary **dynamic environment**, which is created specifically for that feature branch. This dynamic environment is used for testing.
3. **Testing and Deployment Pipeline**:
   - The code is tested thoroughly in this dynamic environment.
   - Once the tests pass and the feature is approved, the next steps are executed:
     - **Merging** the feature branch into the `main` branch.
     - Deploying to **staging** for further testing (if applicable).
     - Deploying to **production** for end-users.

This process works well for small-scale projects or limited teams. However, challenges arise when dealing with **larger organizations**.

---

### The Problem:

In a large organization, multiple teams with several developers may work on the same project simultaneously. This means:
- For every new feature or code development, **a separate dynamic environment is created automatically**.
- While this solves the problem of isolating feature development and testing, it introduces a **new issue**: 
  - **Accumulation of unused dynamic environments.**

Here’s what happens:
- Once a feature branch is merged into the `main` branch and tested, the corresponding dynamic environment remains active unless explicitly deleted.
- These idle environments can lead to:
  - **Resource wastage**: Unused environments consume significant resources, such as disk storage, CPU power, memory, and networking bandwidth.
  - **Increased Costs**: In our example, we are using **Heroku Cloud** to host these environments. Idle environments mean unnecessary charges, impacting the project budget.

---

### Manual Deletion of Dynamic Environments:

A **manual solution** to this issue would be to:
1. Log in to the **Heroku Dashboard** or use the **CLI**.
2. Identify unused dynamic environments.
3. Delete each of them manually.

While this is technically possible, it is impractical and contradicts the principle of **automation** in modern CI/CD pipelines.

---

### Automated Solution: Stop Environment

To address this issue, we can automate the deletion of idle dynamic environments. GitLab provides features to handle this within the **pipeline itself**. The solution involves introducing a concept called **"Stop Environment"**.

#### Steps to Implement Stop Environment:
1. **Add a New Job**:
   - This job will:
     - Identify idle dynamic environments.
     - Free up resources by deleting the corresponding applications from Heroku.
2. **Modify the Pipeline**:
   - Add additional code to implement the **Stop Environment** logic.
   - Configure settings in the **GitLab UI** to integrate this behavior.

---

### Coding the Stop Environment Job:

#### Purpose of the Job:
The job's primary goal is to **tear down the dynamic environment** created for the feature branch. In our case, this involves:
- Destroying the Heroku application associated with the dynamic environment.
- Releasing all associated resources (e.g., storage, application data, Heroku URLs).

#### Implementing the Job:

To destroy a Heroku application, we use the **Alpine Heroku CLI Docker image**, just as we did for the deploy job.

Here’s the **script structure**:

1. **Use Heroku CLI**:
   - The `heroku apps:destroy` command is used to delete the application.
   - Example:
     ```bash
     heroku apps:destroy --app $FEATURE_APP --confirm $FEATURE_APP
     ```
   - In this command:
     - `$FEATURE_APP` is a variable storing the application name.
     - The `--confirm` flag is used to confirm the deletion (Heroku requires this to prevent accidental deletions).

2. **Export Variables**:
   - The application name (`$FEATURE_APP`) needs to be exported in the `before_script` section of the pipeline.
   - Example:
     ```bash
     export FEATURE_APP=my-dynamic-app-name
     ```

3. **Destroy the Application**:
   - The job should specify the same **environment name** as the one created during the `deploy feature` job to ensure consistency.
   - Additionally, log messages (e.g., `echo "Destroying dynamic environment for $FEATURE_APP"`) can be added for better visibility during pipeline execution.

#### Irreversibility of Deletion:
It’s crucial to note that deleting an application is **irreversible**. Once destroyed, all resources are permanently removed.

---

### Next Steps:

#### When to Run the Stop Environment Job:
Deciding **when to execute this job** is critical. Here are some considerations:
- The job could run:
  - Immediately after the **merge** of the feature branch into the main branch.
  - At a **specific pipeline stage**, such as after the staging or production deployment.
  - As part of a **scheduled cleanup pipeline** (e.g., periodically scanning and removing idle environments).

#### Factors to Evaluate:
- Dependencies: Ensure the feature branch is fully merged and no further testing is required before tearing down its dynamic environment.
- Timing: Running the job too early might disrupt ongoing testing or QA processes.
- Resource Monitoring: Implement checks to ensure only idle environments are deleted.

---

### Conclusion:

By introducing a **Stop Environment** job, we achieve:
- Automation: Eliminating manual intervention for cleanup.
- Cost Optimization: Reducing idle resource consumption and associated costs.
- Scalability: Supporting larger teams and multiple concurrent feature branches.

Think about the best stage or sequence to execute the **Stop Environment** job in your pipeline. We'll discuss possible approaches in the next lecture.