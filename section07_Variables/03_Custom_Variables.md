### Custom Variables in GitLab CI/CD

For specific needs, GitLab CI/CD allows you to define **custom variables**. These variables can store reusable information or sensitive data depending on the requirements of your pipeline.  

There are multiple ways to set up custom variables:
1. **In the `.gitlab-ci.yml` file**:  
   Ideal for reusable configurations and non-sensitive data.
2. **In the project settings (GitLab UI)**:  
   Suitable for storing sensitive information like passwords or tokens securely, where you don’t want the values to appear in the pipeline file.  

#### Defining Custom Variables in the `.gitlab-ci.yml` File

Let’s first explore how to define custom variables directly in the `.gitlab-ci.yml` file. These variables are defined at the pipeline level and are available to all jobs unless overridden.

1. **Defining Variables**  
   To declare variables in the `.gitlab-ci.yml` file, use the `variables` keyword. Then define variables as key-value pairs. For example:

   ```yaml
   variables:
     NAME: "John"
     MESSAGE: "Hello, $NAME. How are you?"
   ```

   - `NAME` is the variable holding the value `"John"`.
   - `MESSAGE` uses the value of `NAME` to construct a dynamic message.

2. **Using Variables in a Job**  
   To use these variables, reference them with the `$` symbol in the `script` section of a job.  

   Example:
   ```yaml
   display_message_job:
     script:
       - echo "$MESSAGE"
   ```

3. **Pipeline Output**  
   When you commit and execute the pipeline, the job will output:  
   ```
   Hello, John. How are you?
   ```

#### Variable Scope and Overriding

Custom variables defined at the pipeline level are **global**, meaning they are available to all jobs in the pipeline. However, variables can be overridden within individual jobs.

1. **Overriding Variables in a Job**  
   If you redefine a variable within a specific job, it overrides the global value for that job only.

   Example:
   ```yaml
   variables:
     NAME: "John"
     MESSAGE: "Hello, $NAME. How are you?"

   override_message_job:
     script:
       - echo "$MESSAGE"
     variables:
       NAME: "Mark"
   ```

   - For the `override_message_job`, the variable `NAME` will be overridden to `"Mark"`.
   - The output for this job will be:  
     ```
     Hello, Mark. How are you?
     ```

2. **Impact on Other Jobs**  
   The override affects only the specific job where the variable is redefined. Other jobs in the pipeline will still use the global value.

#### Why Use Custom Variables in the `.gitlab-ci.yml` File?

Custom variables are particularly useful for:
1. **Reusability**:  
   Instead of hardcoding values like URLs, file paths, or configuration options multiple times, you can define a variable once and reference it throughout the pipeline.
   - Example:
     ```yaml
     variables:
       BASE_URL: "https://api.example.com"

     job1:
       script:
         - curl "$BASE_URL/endpoint1"

     job2:
       script:
         - curl "$BASE_URL/endpoint2"
     ```

2. **Minimizing Errors**:  
   By centralizing variable definitions, you reduce the risk of typos or inconsistencies when updating values.

#### Handling Sensitive Information

While defining variables directly in the `.gitlab-ci.yml` file is convenient, it is not recommended for sensitive information like passwords, API keys, or tokens. Anyone with access to the repository can view these values.  

For such cases, GitLab provides a secure way to store variables in the **project settings**. Variables defined in this way are:
- **Encrypted**: They are stored securely and not exposed in the code.
- **Hidden in Logs**: Sensitive values are masked in pipeline logs, preventing accidental exposure.

---

### Next Steps

In the next lecture, we will explore how to securely store sensitive variables in the **project settings** using the GitLab UI. This approach ensures secure handling of passwords, tokens, and other confidential information.

