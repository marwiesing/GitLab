### Predefined Variables in GitLab CI/CD

Let's begin with **predefined variables**.  

GitLab CI/CD provides a default set of **predefined CI/CD variables** that are available in every pipeline. These variables are built-in and require no additional configuration to use. They provide valuable information about the GitLab environment, pipeline, and repository.  

You can use predefined variables to access details like:
- Usernames
- Branch names
- Commit IDs
- Pipeline metadata  
...and much more.  

#### Where to Find Predefined Variables

To view the complete list of available predefined variables, refer to the [official GitLab documentation](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html). (This link should also be available in the **Resources** tab of this lecture.)  

Each variable in the documentation includes:
1. The **variable name** (e.g., `CI_COMMIT_SHA`).
2. A **description** of the variable's purpose.
3. The **minimum GitLab and GitLab Runner versions** required for compatibility.

#### Common Predefined Variables

Here are a few examples of predefined variables and their purposes:

1. **`CI_COMMIT_MESSAGE`**:  
   Stores the commit message you provided when committing changes to a branch.

2. **`CI_COMMIT_SHA`**:  
   Provides the full commit revision number (a long hash).

3. **`CI_COMMIT_SHORT_SHA`**:  
   Returns the first eight characters of the commit revision number, useful for shorter references.

4. **`CI_JOB_NAME`**:  
   Stores the name of the current job.

5. **`CI_COMMIT_REF_SLUG`**:  
   Creates a URL-safe version of the branch name. This is often used for creating caches or tags. For example, each branch can have its own cache to speed up subsequent pipeline runs.

These variables are incredibly useful when writing pipelines, as they allow you to dynamically adapt to the environment or project state.

#### Practical Uses of Predefined Variables

Here are a few examples of how you might use predefined variables in real-world scenarios:
1. **Docker Image Tagging**:  
   Use `CI_COMMIT_SHORT_SHA` to tag a Docker image with the current commit's short SHA. This ensures traceability between the code and the container image.

   ```yaml
   script:
     - docker build -t my-image:$CI_COMMIT_SHORT_SHA .
   ```

2. **Branch-Specific Caching**:  
   Use `CI_COMMIT_REF_SLUG` to define a cache key for each branch, ensuring that pipeline jobs on different branches do not interfere with each other's caches.

   ```yaml
   cache:
     key: "$CI_COMMIT_REF_SLUG"
     paths:
       - node_modules/
   ```

#### Using Predefined Variables in a Pipeline

Let’s see how to use predefined variables in practice by creating a simple job in the `.gitlab-ci.yml` file.

```yaml
demo_job:
  script:
    # Print the commit message
    - echo "Commit Message: $CI_COMMIT_MESSAGE"
    # Print the job name
    - echo "Job Name: $CI_JOB_NAME"
```

**Explanation**:
- **`$CI_COMMIT_MESSAGE`**: Outputs the commit message provided during the commit.
- **`$CI_JOB_NAME`**: Outputs the name of the current job (in this case, `demo_job`).

#### Running the Pipeline

After committing the changes and pushing them to the repository:
1. Navigate to the **Pipelines** section in GitLab.
2. Trigger the pipeline and wait for it to complete.
3. Check the job logs.  

The output should look something like this:
```
Commit Message: This is a sample commit message
Job Name: demo_job
```

This demonstrates how the predefined variables dynamically adapt to the context of the pipeline.

#### Behind the Scenes

Here’s what happens internally:
1. GitLab reads the `.gitlab-ci.yml` file and processes the pipeline.
2. Predefined variables are exposed to the GitLab Runner during job execution.
3. The Runner replaces the variable placeholders (e.g., `$CI_COMMIT_MESSAGE`) with their actual values.
4. The script executes with the resolved values.

#### Why Use Predefined Variables?

Predefined variables make pipelines dynamic and environment-aware, reducing the need for hardcoding values and allowing for greater flexibility. As you progress in creating real-world pipelines, these variables will be invaluable for tasks like:
- Generating dynamic filenames.
- Customizing behavior based on branches or commits.
- Managing caches or environment-specific configurations.

#### Next Steps

This was a brief introduction to predefined variables in GitLab CI/CD. I encourage you to explore the full list of predefined variables in the [GitLab documentation](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html). Understanding these variables will make your pipeline scripts more robust and versatile.

In the next lecture, we will dive into **custom variables** and see how you can define variables tailored to your project's specific needs.


