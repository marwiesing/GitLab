### Detailed Explanation of Job Templating with YAML Anchors

In this example, we’ll take a simple pipeline consisting of two jobs: **Job One** and **Job Two**. If you look closely, you’ll notice that both jobs share some common YAML configuration. For instance:

1. Both jobs use the **same Python image**.
2. Both jobs include a **before_script** section to install dependencies from a `requirements.txt` file.
3. Both jobs are configured to run **only on the `main` branch**.

This repetitive configuration provides a perfect opportunity to optimize the pipeline using **YAML anchors**. 

---

#### Step-by-Step: Creating a Job Template with YAML Anchors

1. **Identify and Extract the Common Block**:  
   Whenever you notice that multiple jobs share similar configurations, the first step is to identify the common block of code. In this case, the shared configuration includes:
   - The Python image
   - The `before_script` steps
   - The condition to run the job only on the `main` branch

   You can now cut out this block from both jobs.

2. **Define a Disabled Job**:  
   To make this common block reusable, you need to create a **disabled job**. In GitLab CI/CD, a job is disabled by adding a **dot (`.`)** in front of the job name. This prevents the job from being executed when the pipeline runs, effectively making it an **idle job** or **template job**.

   For example:
   ```yaml
   .job_template: &template
     image: python:3.9
     before_script:
       - pip install -r requirements.txt
     only:
       - main
   ```
   - **Disabled Job**: `.job_template` ensures this is not executed as part of the pipeline.
   - **Anchor Definition**: The `&template` creates an **anchor**, a named reference for this block of configuration.

3. **Reuse the Template with Aliases**:  
   To reuse the common block in other jobs, you use its **alias**. This is done with the `<<` merge key in YAML. The alias is referenced using the **`*`** character followed by the anchor name.

   Here’s how the two jobs would now look:
   ```yaml
   job_one:
     <<: *template
     script:
       - python run_job_one.py

   job_two:
     <<: *template
     script:
       - python run_job_two.py
   ```

   - **Merge Key**: `<<` indicates that the key-value pairs from the `*template` alias should be merged into the current job.
   - **Anchor Usage**: `*template` references the common block defined earlier.

4. **Pipeline Execution**:  
   When the pipeline runs, the configuration from the `template` anchor is merged into `job_one` and `job_two`. It is as if the shared configuration were written directly into each job, but without duplication in the YAML file.

---

### Explanation of YAML Anchors and Aliases
- **Anchor (`&`)**: This creates a reference for a block of YAML code that can be reused later. The syntax is `&anchor_name`.
- **Alias (`*`)**: This calls the reference defined by the anchor. The syntax is `*anchor_name`.
- **Merge Key (`<<`)**: This special key allows you to merge the referenced configuration into the current block.

For example:
```yaml
.common-config: &common
  image: python:3.9
  before_script:
    - echo "Preparing environment"
    - pip install -r requirements.txt

job_one:
  <<: *common
  script:
    - python run_job_one.py

job_two:
  <<: *common
  script:
    - python run_job_two.py
```

---

### Advantages of This Approach
1. **Code Reusability**: The common configuration is defined once and reused across multiple jobs, following the **DRY (Don’t Repeat Yourself)** principle.
2. **Simpler Maintenance**: Any updates to the shared configuration (e.g., changing the Python version) can be made in one place, reducing the risk of inconsistencies.
3. **Reduced Pipeline Length**: The YAML file becomes cleaner and easier to read.
4. **Error Prevention**: By avoiding duplication, you reduce the chances of introducing typos or mismatches in the configuration.

---

### A Closer Look at the Final Pipeline
Using the example above, here’s the complete pipeline:

```yaml
stages:
  - prepare
  - test

# Disabled job (template) with an anchor
.common-template: &template
  image: python:3.9
  before_script:
    - pip install -r requirements.txt
  only:
    - main

# Job One
job_one:
  <<: *template
  stage: prepare
  script:
    - python job_one.py

# Job Two
job_two:
  <<: *template
  stage: test
  script:
    - python job_two.py
```

---

### Notes on Job Templating
- **Flexibility**: You can extend or override parts of the template in individual jobs. For example, if `job_two` requires additional steps in the `before_script` section, you can define them without affecting the template:
  ```yaml
  job_two:
    <<: *template
    before_script:
      - pip install -r requirements.txt
      - echo "Additional setup for job_two"
    script:
      - python job_two.py
  ```

- **Nested Templates**: You can even combine multiple templates using anchors and aliases. This is useful when your pipeline has varying degrees of shared configurations.

---

### Looking Ahead
Now that we’ve learned how to use YAML anchors to create job templates, we’ll apply this concept to the actual pipeline for the project. In the next lecture, we’ll take the existing pipeline code and refactor it to eliminate redundancy, making it cleaner and more maintainable.

By the end, you’ll have a practical understanding of how job templating can simplify pipeline management for real-world projects.