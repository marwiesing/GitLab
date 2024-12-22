### Exploring Runners in the GitLab UI

Now, let’s dive into the GitLab UI to explore where and how we can access and manage runners. 

- **Navigating to Runners**:
   - Under the **Project Settings**, go to the **CI/CD** section.
   - Scroll down to find the **Runners** section. This is where you can view and manage the runners available for your project.

#### What is GitLab Runner?

- **GitLab Runners** are processes that execute **CI/CD jobs** in GitLab. These jobs are isolated and run in separate virtual machines or containers. Runners are integral to the CI/CD workflow, ensuring that pipeline tasks, such as building, testing, and deploying code, are performed efficiently and securely.
- GitLab Runner is an **open-source application** written in **Go**. It is used to pick up and execute CI/CD jobs defined in `.gitlab-ci.yml`.
- Runners can be registered:
  - On **separate servers**.
  - As **separate users**.
  - On a **local machine** for testing and development.

---

### Runner States

Runners can exist in one of two states:
1. **Active**: Available and ready to execute jobs.
2. **Paused**: Temporarily unavailable to run jobs.

---

### Types of Runners

#### **1. Project Runners**
- These runners are **specific to a single project**.
- They are useful for scenarios where a project requires specialized environments or configurations.

#### **2. Instance Runners**
- These runners are **available to all projects and groups** in the GitLab instance.
- They are shared across multiple projects, providing flexibility and scalability.
- Example: In the current setup, **108 instance runners** are available for use.

---

### Tags and Runner Selection

**Tags** play a critical role in how jobs are assigned to runners:
- Tags help specify which type of jobs a runner can handle.
- When a job is defined in `.gitlab-ci.yml`, tags can be added to ensure the job is picked up by an appropriate runner. For example:
  ```yaml
  build-job:
    tags:
      - docker
    script:
      - echo "Building Docker image"
  ```
- If no tags are specified, GitLab selects a runner from the available pool.

#### Example of Tagged Runners:
- Runners with tags like `docker`, `windows`, or `default` indicate their specific capabilities or environments.
- Multiple tags can be assigned to a single job for more precise runner selection.

---

### Shared Runners: An Updated View

The current list of shared runners includes multiple configurations for different purposes, such as:

1. **Standard Runners**:
   - Tagged with `default`.
   - Example:
     ```
     #12270807 (j1aLDqxS)
     1-blue.saas-linux-small-amd64.runners-manager.gitlab.com/default
     ```

2. **Docker-in-Docker (dind) Runners**:
   - Tagged with `dind`, optimized for building Docker images.
   - Example:
     ```
     #11728715 (G3sQ9GXi)
     1-blue.shared-gitlab-org.runners-manager.gitlab.com/dind
     ```

3. **Environment-Specific Runners**:
   - Tagged with `windows`, `linux`, `amd64`, etc., to indicate the specific operating system or architecture.

---

### Using Shared Runners in Pipelines

To demonstrate using a shared runner, consider the following pipeline configuration:

1. Define a job in `.gitlab-ci.yml`:
   ```yaml
   windows-info:
     tags:
       - windows
     script:
       - systeminfo
   ```
   - The `tags` directive ensures the job is executed on a runner with the `windows` tag.
   - The `systeminfo` command outputs detailed system configuration information of the Windows host.

2. When the pipeline runs, the job is assigned to a runner based on the specified tag.

3. Output logs include:
   - The runner ID used to execute the job.
   - System details of the virtual machine or container.

---

### Key Updates in Runner Management

1. **Enable/Disable Instance Runners for a Project**:
   - Instance runners can be enabled or disabled at the project level, giving teams control over which runners are used.

2. **Expanded Runner Options**:
   - The updated GitLab UI provides a comprehensive list of available runners, with details like:
     - Runner IDs.
     - Tags (e.g., `default`, `docker`, `windows`).
     - Hostnames.

3. **Dynamic Scaling**:
   - Instance runners now support a wider range of configurations, including specialized runners for tasks like Docker builds or platform-specific jobs.

---

### Next Steps

In the upcoming sections:
- We will explore how to set up a **custom runner** on a local machine.
- Learn how to register and configure the runner.
- Run pipelines with both shared and specific runners to compare their functionality and performance.

Stay tuned for hands-on examples and advanced configuration tips!

