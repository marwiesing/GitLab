Tags in GitLab CI/CD play a vital role in controlling **which runner** executes a job. Let’s break it down so you can understand both the use cases and the advantages of using tags versus having no tags.

---

### **What Happens Without a Tag?**
When you don’t specify a tag in your `.gitlab-ci.yml` file:
1. GitLab will try to use any **available runner**:
   - It could be a **shared runner** (provided by GitLab or your instance).
   - It could be a **specific runner** (registered for your project or group).
2. This approach works well in simple setups where:
   - Runners are generic.
   - All jobs can be executed by any available runner.

**Example**:
Your provided `.gitlab-ci.yml` file:
```yaml
image: docker:latest
services:
  - docker:dind
```
- This worked without tags because GitLab likely used a **shared runner** configured with `docker` and `docker:dind` support.

---

### **What Happens With Tags?**
When you specify a tag in your `.gitlab-ci.yml` file:
1. GitLab **matches the job** to a runner with the **same tag**.
2. This ensures that only **specific runners** execute your job.

**Example**:
```yaml
build:
  tags:
    - docker
  script:
    - docker-compose build
```
- GitLab looks for a runner with the tag `docker` to execute this job.

---

### **Advantages of Using Tags**

#### 1. **Target Specific Runners**
- Tags allow you to direct jobs to runners with the right environment or capabilities.
- Example:
  - A runner tagged with `docker` might have Docker installed.
  - A runner tagged with `windows` might be a Windows-based runner.

#### 2. **Optimize Performance**
- By using tags, you avoid unnecessary job queueing. Jobs are matched to the most relevant runners instead of being randomly assigned.

#### 3. **Control Resource Usage**
- Tags let you restrict jobs to specific infrastructure.
- Example:
  - A powerful GPU-enabled runner might have the tag `gpu`. Only GPU jobs would run on that runner.

#### 4. **Isolate Sensitive Jobs**
- Tags help direct sensitive jobs to secure runners.
- Example:
  - Runners with access to private secrets or environments (e.g., `production`) can be tagged accordingly.

#### 5. **Flexibility in Multi-Environment Pipelines**
- Tags allow you to design pipelines for **heterogeneous environments**.
- Example:
  - A `linux` tag for jobs on Linux machines.
  - A `windows` tag for jobs on Windows machines.

#### 6. **Improved Troubleshooting**
- Tags make it easier to identify which runner executed a job, aiding debugging efforts.

---

### **When You Might Not Need Tags**
If your project is small and:
- You rely entirely on shared runners.
- Your jobs are generic and don’t require specific configurations or environments.
- You don’t mind the randomness of runner assignment.

In these cases, using no tags is fine and keeps things simple.

---

### **Best Practices for Tags**

1. **Define Meaningful Tags**:
   - Use descriptive tags like `docker`, `linux`, `gpu`, or `production` to indicate the runner’s environment or purpose.

2. **Keep Jobs Modular**:
   - Assign tags only to jobs that truly require them. Avoid over-tagging to maintain flexibility.

3. **Monitor Runner Availability**:
   - Ensure runners with specific tags are **active** and **online**.

---

### **Your Case: Docker Example**

If you decide to use tags for your Docker-based pipeline:
1. **Assign a Tag**: Ensure there’s a runner with the `docker` tag.
2. **Update `.gitlab-ci.yml`**:
   ```yaml
   build:
     tags:
       - docker
     script:
       - docker-compose build
   ```

This guarantees the job will only run on runners capable of handling Docker jobs, ensuring consistency and performance.

---

**Conclusion**:  
Tags offer **control**, **scalability**, and **customization**, especially in larger, multi-environment setups. However, for smaller projects relying on shared runners, skipping tags can simplify the configuration. Let me know if you’d like to explore specific examples or scenarios further!