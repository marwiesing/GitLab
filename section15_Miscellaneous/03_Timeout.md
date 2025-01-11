## Configuring Timeouts in GitLab Pipelines

### Introduction

In GitLab CI/CD, **timeout settings** define the **maximum time** a job can execute before it is automatically marked as failed. This feature prevents jobs from running indefinitely due to issues like deadlocks, infinite loops, or unexpected delays.

Timeout settings are crucial for optimizing resource utilization and ensuring pipeline reliability.

---

### Types of Timeouts in GitLab

GitLab offers three levels of timeouts:

1. **Job-Level Timeout**:
   - Configured within the `.gitlab-ci.yml` file for individual jobs.
   - Specifies the maximum execution time for a specific job.
   - Useful for limiting jobs expected to complete within seconds or minutes but may hang due to unforeseen issues.

2. **Runner-Specific Timeout**:
   - Configured for each GitLab Runner in the runner’s settings.
   - Applies a global timeout limit to all jobs executed by that runner.
   - Prevents shared runners from being overwhelmed by projects with excessively long-running jobs (e.g., jobs running for days).

3. **Project-Level Timeout**:
   - Configured in the **Project Settings**.
   - Defines the default timeout for all jobs in the project.
   - Jobs without explicitly defined timeouts inherit this value.

---

### Hierarchy and Precedence of Timeouts

- **Runner-Specific Timeout**: Sets the hard upper limit for any job.
- **Job-Level Timeout**: Overrides the project-level timeout for specific jobs.
- **Project-Level Timeout**: Acts as a fallback for jobs without a defined timeout.

> **Note**: A job-level timeout cannot exceed the runner-specific timeout. If it does, the job will still be terminated at the runner’s timeout value.

---

### Default Timeout in GitLab

- By default, GitLab sets the timeout to **1 hour (60 minutes)** for all jobs and projects.
- You can adjust this default timeout based on your project’s requirements.

---

### Configuring Timeouts: Step-by-Step

#### 1. **Default Timeout Behavior**

If no timeout is explicitly set:
- The pipeline uses the project-level timeout.
- If no project-level timeout is defined, the default 1-hour timeout applies.

#### Example Pipeline with Default Timeout:
```yaml
stages:
  - example

example_job:
  script:
    - echo "Starting job..."
    - sleep 120
    - echo "Job complete."
```
- **Behavior**:
  - Since the job takes 2 minutes (`sleep 120`), it completes successfully within the default timeout.

---

#### 2. **Setting Project-Level Timeout**

1. Navigate to **Settings** > **CI/CD** > **General Pipelines** in your project.
2. Expand the **Timeout** section.
3. Adjust the value:
   - The minimum timeout is **10 minutes**.
   - The maximum timeout is **1 month**.

#### Example Use Case:
- If a job is expected to run for a long time (e.g., 30 minutes), you can increase the project timeout accordingly.
- Conversely, if you want to enforce stricter limits, reduce the project timeout.

---

#### 3. **Setting Job-Level Timeout**

You can define a timeout for individual jobs directly in the `.gitlab-ci.yml` file using the `timeout` keyword.

#### Example:
```yaml
stages:
  - example

job_one:
  script:
    - echo "Job 1"
    - sleep 900 # Sleep for 15 minutes
  timeout: 10m # Job-level timeout (10 minutes)

job_two:
  script:
    - echo "Job 2"
    - sleep 900 # Sleep for 15 minutes
  timeout: 20m # Job-level timeout (20 minutes)
```

- **Behavior**:
  - `job_one` fails after 10 minutes if not completed.
  - `job_two` fails after 20 minutes if not completed.

> **Note**: If a job-level timeout is not specified, the project-level timeout applies.

---

#### 4. **Setting Runner-Specific Timeout**

Runner-specific timeouts are configured by modifying the runner’s configuration file (`config.toml`).

Example Configuration:
```toml
[[runners]]
  name = "Example Runner"
  executor = "docker"
  limit = 1
  timeout = "30m" # Runner-specific timeout
```

- This sets the maximum timeout for jobs executed by this runner to **30 minutes**.
- Any job with a timeout exceeding this value will still be terminated at 30 minutes.

---

### Timeout Precedence Example

#### Configuration:
1. Runner-specific timeout: **30 minutes**.
2. Project-level timeout: **15 minutes**.
3. Job-level timeout for `job_two`: **20 minutes**.

#### Behavior:
- **job_one**: Follows the project-level timeout (15 minutes).
- **job_two**: Uses the job-level timeout (20 minutes), but the runner enforces a maximum timeout of 30 minutes.

---

### Example Pipeline to Demonstrate Timeout

```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Building..."
    - sleep 1200 # 20 minutes sleep
  timeout: 25m # Job-level timeout (overrides project-level)

test_job:
  stage: test
  script:
    - echo "Testing..."
    - sleep 600 # 10 minutes sleep
  timeout: 10m # Job-level timeout

long_running_job:
  stage: build
  script:
    - echo "Long job..."
    - sleep 7200 # 2 hours sleep
```

1. **build_job**:
   - Timeout: 25 minutes (job-level).
   - Completes if it finishes within 25 minutes.
2. **test_job**:
   - Timeout: 10 minutes (job-level).
   - Fails if it runs longer than 10 minutes.
3. **long_running_job**:
   - Timeout: Defaults to the project-level value if no job-level timeout is specified.

---

### Key Points to Remember

1. **Minimum Timeout**:
   - Project-level timeout must be **at least 10 minutes**.

2. **Maximum Timeout**:
   - A job cannot exceed the **runner-specific timeout**.

3. **Default Behavior**:
   - If no job-level timeout is defined, the project-level timeout applies.

4. **Timeout vs. Resources**:
   - Long timeouts can lead to resource overuse. Use them judiciously.

5. **Deadlock Prevention**:
   - Timeouts safeguard against jobs stuck in infinite loops or waiting indefinitely for resources.

---

### Final Thoughts

Timeout settings in GitLab provide fine-grained control over job execution. By combining job-level, project-level, and runner-specific timeouts, you can ensure efficient resource usage and prevent jobs from running excessively long.

Take time to configure appropriate timeout values based on your project needs. This will help maintain a healthy and reliable CI/CD pipeline.

See you in the next class!