### Evaluating Possible Placement of the Stop Feature Job:

#### **Option 1**: Place the job in a new stage after the `deploy feature` stage.
- This approach involves creating a new stage, say `stop feature`, and placing it immediately after the `deploy feature` stage.
- **Why this doesn't work**:
  - If the stop feature job runs right after deployment, the dynamic environment will be destroyed **immediately after it is created**.
  - This would make the application unavailable for the subsequent **test feature** stage.
  - As a result, the `test feature` job would fail because it wouldn’t find the application running in the dynamic environment.
- **Conclusion**: This configuration is invalid.

#### **Option 2**: Place the job after the automated feature testing stage.
- The idea here is to destroy the application only after the automated testing of the deployed feature is complete.
- **Why this doesn't work**:
  - Although this seems logical, it would prematurely destroy the environment, making the live application inaccessible.
  - Developers and reviewers often need to **manually review** the application in the dynamic environment before approving it.
  - Destroying the environment immediately after automated tests would hinder this manual review process.
- **Conclusion**: This option is also void.

---

### The Ideal Placement:

The most appropriate time to run the **stop feature** job is:
- **After the feature branch is merged into the main branch and deleted.**

#### Why this works:
1. **Preserving the dynamic environment**:
   - The dynamic environment remains live during:
     - Automated testing.
     - Manual review by developers or maintainers.
   - This ensures the maintainer has a chance to test and verify the application in its live dynamic environment.
2. **Cleanup after merge**:
   - Once the maintainer approves and merges the branch into the `main`, the feature branch is deleted.
   - At this point, the dynamic environment associated with the feature branch is no longer needed and can be safely destroyed.

---

### Implementing Stop Environment in the Pipeline:

#### How to Configure the Pipeline:

To automate this behavior, we need to use GitLab's **on_stop** keyword under the `environment` section of the **deploy feature** job. Here's how it works:

1. **Linking the Deploy and Stop Feature Jobs**:
   - The `deploy feature` job is responsible for creating the dynamic environment.
   - Under the `environment` tag of the `deploy feature` job, add:
     ```yaml
     on_stop: stop_feature
     ```
   - This configuration:
     - Links the **deploy feature** job to the **stop feature** job.
     - Ensures that the **stop feature** job is triggered when the associated branch is deleted.

2. **Defining the Stop Feature Job**:
   - Create the **stop feature** job in the pipeline.
   - Use the `action: stop` keyword under the `environment` tag:
     ```yaml
     environment:
       name: feature/$CI_COMMIT_REF_NAME
       action: stop
     ```
   - **Purpose**:
     - This indicates that the job is responsible for stopping the environment created by the `deploy feature` job.
     - It prevents GitLab from treating the `stop feature` job like a regular job in the pipeline.

3. **Using the `git strategy: none` Variable**:
   - When the feature branch is deleted, GitLab runners should **not** try to check out the branch code for the `stop feature` job. 
   - To prevent this, set the `git strategy` to `none`:
     ```yaml
     variables:
       GIT_STRATEGY: none
     ```

4. **Adding a Manual Trigger**:
   - To ensure the **stop feature** job runs only after a maintainer manually approves the merge request, add a `when: manual` condition:
     ```yaml
     when: manual
     ```
   - **Result**:
     - The job will only execute after someone manually triggers it from the GitLab UI during or after the merge request process.

---

### Flow of Stop Environment:

Here’s a step-by-step explanation of how the process works:

1. **Deploy Feature Job**:
   - Creates a dynamic environment on Heroku for the feature branch.
   - Defines the `on_stop: stop_feature` configuration to link to the `stop feature` job.

2. **Manual Review and Testing**:
   - Developers and reviewers manually test the live application in the dynamic environment.
   - Automated tests are also executed during this stage.

3. **Merge Request Approval**:
   - The maintainer reviews the merge request and approves it.
   - The feature branch is merged into the `main` branch and deleted.

4. **Trigger Stop Feature Job**:
   - When the branch is deleted, the `on_stop` action triggers the **stop feature** job.
   - The job uses the `heroku apps:destroy` command to:
     - Delete the Heroku application.
     - Free up all associated resources (e.g., storage, application data, URLs).

---

### Key Considerations:

- **Stage Placement**:
  - The `stop feature` job must be in the **same stage** as the `deploy feature` job.
  - This ensures that GitLab doesn’t skip the `stop feature` job if it’s in a later stage.

- **Pipeline Behavior**:
  - Jobs with the `action: stop` keyword behave differently than regular jobs.
  - GitLab recognizes these jobs as environment-specific cleanup jobs and handles them accordingly.

---

### Summary:

With this configuration:
- The **stop feature** job is seamlessly integrated into the pipeline.
- The dynamic environment remains accessible for testing and manual review until the feature branch is merged and deleted.
- The cleanup process is automated, freeing resources without manual intervention.

In the next lecture, we’ll discuss **additional settings in GitLab** to ensure the proper functioning of the Stop Environment feature.

Thank you!