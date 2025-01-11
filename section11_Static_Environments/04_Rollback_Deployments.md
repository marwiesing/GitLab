### **Summary: Deployment Rollbacks in GitLab**

This lecture explains how **deployment rollbacks** work in GitLab and how to ensure they function correctly by properly configuring the pipeline in the `gitlab-ci.yml` file.

---

### **Key Points**

1. **Rollback Basics:**
   - Clicking the rollback button in GitLab does **not rerun the entire pipeline**; it only reruns the specific stage where the environment is defined (e.g., the `deploy` stage).
   - Rollbacks depend on the **stored Docker images** and the way they are tagged.

2. **Rollback Issue Example:**
   - In the example, a rollback was performed on an application with the "Welcome to Employee Portal" header.
   - The rollback ran successfully, but the application did not revert to the previous version because:
     - The Docker image for the previous commit was **overwritten** by the newer pipeline run due to the image tagging strategy.

3. **Root Cause of the Problem:**
   - The Docker images were tagged with the branch name (using the variable `CI_COMMIT_REF_SLUG`), causing the latest image to overwrite the previous one.
   - As a result, rollbacks pulled the same image, which did not reflect the desired previous version.

4. **Solution: Unique Image Tags:**
   - Change the image tagging strategy to use commit-specific tags by replacing `CI_COMMIT_REF_SLUG` with `CI_COMMIT_SHORT_SHA` (the first 8 characters of the commit hash).
   - This ensures each pipeline generates a **unique Docker image tag** for every commit, preserving a history of images.

5. **Updated Workflow:**
   - With the new tagging strategy:
     - Each Docker image is stored in the container registry with a unique tag based on the commit ID.
     - Rollbacks now fetch the correct image corresponding to the commit ID, allowing for a proper rollback.

6. **Demonstration of Success:**
   - After implementing the fix:
     - Multiple Docker images were created with unique tags for each commit.
     - A rollback to a previous commit successfully restored the application to its earlier state.

7. **Importance of Rollbacks:**
   - Rollbacks are critical for mitigating issues in production or staging environments.
   - Proper pipeline design and image tagging ensure rollbacks function as intended.

---

### **Conclusion**
Deployment rollbacks in GitLab require:
1. Proper pipeline configuration.
2. Unique Docker image tags for every commit.
3. A clear strategy for maintaining image history in the container registry.

This ensures reliable rollbacks and minimizes downtime when reverting to a previous deployment.