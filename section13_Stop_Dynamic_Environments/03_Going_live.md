### Welcome back!

We have now completed setting up the **CI/CD pipeline** for managing dynamic environments with the **Stop Environment** feature. However, there’s one final configuration step we need to address before pushing our changes to the repository.

---

### Unprotecting Feature Branches

#### Why do we need to unprotect feature branches?
The **Stop Environment** functionality depends on the "delete branch on merge" option, which appears on the GitLab merge page. However:
- **Protected branches** do not display this "delete on merge" checkbox by default.
- Without this option enabled, the Stop Environment job will not trigger when a branch is deleted after merging.

To fix this, we must **unprotect the feature branches**.

---

### Steps to Unprotect Feature Branches:
1. Navigate to **Settings > Repository > Protected Branches** in GitLab.
2. Locate all branches matching the naming pattern `feature-*`.
3. Unprotect these branches.

Now, all branches starting with the `feature-` prefix are **unprotected**. 

#### Ripple Effect of Unprotecting Branches:
Unprotecting branches has a side effect:
- **Protected variables** (like API keys, secrets, or credentials) are not available in pipelines for unprotected branches.
- To ensure the pipeline works correctly, we also need to **unprotect these variables**.

---

### Pushing the New Branch to the Repository

Once the branches and variables are unprotected:
1. Push the new branch (created for the Stop Environment configuration) to the **remote repository**.
2. Observe the pipeline starting for this new feature branch.

---

### Observing the Stop Feature Job in Action:

1. **Pipeline Behavior**:
   - In the pipeline view, the **Stop Feature** job appears with a different icon, indicating it requires manual action (`when: manual` condition).
   - Hovering over the job shows a message like: `Stop feature requires manual action`.

2. **Stop Environment Button**:
   - Because the `action: stop` keyword is linked to the Stop Feature job, a **"Stop Environment" button** is visible in the GitLab UI.
   - Clicking this button manually triggers the job, which will destroy the dynamic environment.

---

### Testing the Live Application:

Before stopping the environment, you can:
1. Navigate to **Operations > Environments** in the GitLab UI.
2. Look under the **Review** group for your running environment.
3. Click on the dynamic URL to open the live application.

Additionally:
- Cross-check the running application in the **Heroku Dashboard**.
- Ensure the application is functioning as expected.

---

### Creating a Merge Request:

Once you verify that the application is working as expected:
1. Create a **merge request** from the feature branch to the `main` branch.
2. **Important**: Ensure the "Delete branch after merge" checkbox is selected.
   - Selecting this option triggers the Stop Environment job after the branch is merged.

---

### Merge Request Review:

For maintainers:
1. The **Merge Request Review Page** displays:
   - Results from the previous pipeline run.
   - Logs for each job in the pipeline (accessible for further debugging, if needed).
   - A link to the live environment for manual review of the application.
2. The maintainer can:
   - Open the dynamic environment to test the live application.
   - Decide whether to approve or reject the merge request.

---

### Approving and Merging the Request:

1. If the maintainer is satisfied with the application:
   - Approve and merge the request.
   - Ensure the "Delete branch after merge" option is selected.

2. **What happens next**:
   - The feature branch is deleted.
   - The Stop Feature job is triggered automatically, running the `heroku apps:destroy` command to:
     - Remove the dynamic application from Heroku.
     - Free all associated resources (e.g., storage, URLs, application data).

---

### Verifying the Cleanup:

Once the Stop Feature job runs:
1. Check the pipeline logs for the Stop Feature job:
   - The logs show the execution of the `heroku apps:destroy` command and a confirmation message indicating the application was destroyed.
   - Example log:
     ```
     Destroying Heroku application: feature-my-app
     Application successfully destroyed.
     ```

2. Confirm the environment is no longer accessible:
   - Refresh the dynamic application URL, which now shows an error (e.g., "This page isn’t working").
   - Check the **Heroku Dashboard** to verify that the application is deleted.

3. In GitLab, navigate back to **Operations > Environments**:
   - The feature branch environment is no longer listed under "Available Environments."
   - It is now moved to the "Stopped Environments" list.

---

### Benefits of the Stop Environment Configuration:

With this setup:
- **Resource Optimization**: Idle environments are automatically cleaned up after merging, preventing resource wastage.
- **Cost Efficiency**: Eliminates unnecessary expenses, particularly for cloud resources like Heroku.
- **Automation**: The entire process is seamless, from deployment to cleanup, with minimal manual intervention.

---

### Wrapping Up:

This concludes the **Stop Environment** configuration for dynamic environments in the CI/CD pipeline. With this feature:
- You no longer need to worry about unused environments consuming resources or incurring extra costs.
- The pipeline ensures environments are created for testing and automatically destroyed after the feature branch is merged into `main`.

We’ll explore new topics and projects in the next section. See you there! 

---