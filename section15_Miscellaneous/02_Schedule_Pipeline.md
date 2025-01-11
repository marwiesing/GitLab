## Scheduling Pipelines in GitLab

### Introduction

Pipelines in GitLab are typically triggered by specific events, such as:

- Committing changes to a branch.
- Pushing a branch to a repository.
- Merging branches.

However, GitLab provides flexibility beyond event-driven pipelines. You can **schedule pipelines** to run at fixed intervals, making it easy to automate tasks like daily builds, regular tests, or periodic deployments.

In this lecture, we’ll explore how to set up scheduled pipelines in GitLab.

---

### Why Schedule Pipelines?

Scheduled pipelines are useful for:

1. **Automated Maintenance Tasks**:
   - Nightly builds or tests to ensure code stability.
   - Regular deployments or environment refreshes.

2. **Consistent Monitoring**:
   - Running health checks on infrastructure or applications at specific times.

3. **Time-Based Operations**:
   - Executing data processing tasks daily, weekly, or monthly.

---

### How to Schedule Pipelines in GitLab

To configure a scheduled pipeline, follow these steps:

1. **Access the Schedule Section**:
   - Navigate to your GitLab project.
   - Go to **CI/CD** > **Schedules** from the left-hand menu.

2. **Create a New Schedule**:
   - Click **New Schedule** to create a scheduled pipeline.

3. **Configure the Schedule**:
   - **Description**: Provide a descriptive name for the schedule (e.g., "Daily Build" or "Weekly Tests").
   - **Interval**: Define the frequency at which the pipeline will run. GitLab offers:
     - **Predefined intervals**: Daily, Weekly, Monthly.
     - **Custom intervals**: Use **cron syntax** for granular scheduling.

---

### Understanding Cron Syntax

Cron syntax is used to define time intervals for scheduled tasks. If you're unfamiliar with cron, tools like [crontab.guru](https://crontab.guru) can help generate valid cron expressions.

Here are some common examples:

| Interval       | Cron Syntax   | Description                     |
|----------------|---------------|---------------------------------|
| Every minute   | `* * * * *`   | Not allowed in GitLab (min. 60 mins). |
| Every hour     | `0 * * * *`   | Runs at the top of every hour. |
| Every 2 hours  | `0 */2 * * *` | Runs every two hours.          |
| Daily at 11 AM | `0 11 * * *`  | Runs at 11:00 AM daily.        |
| Weekly         | `0 0 * * 0`   | Runs at midnight every Sunday. |

---

### Setting the Time Zone

By default, scheduled pipelines use the **UTC time zone**. You can change this to your local time zone (e.g., EST, PST, etc.) when configuring the schedule.

---

### Specify the Target Branch

Choose the branch for which the pipeline will run. This is usually set to `main` or `master`, but it can also target feature branches if needed.

---

### Optional: Add Custom Variables

You can define custom variables for the scheduled pipeline. These variables override the ones defined in your `.gitlab-ci.yml` file or the project's settings, allowing for flexible pipeline configurations.

---

### Save the Schedule

- Activate the schedule by toggling the **Activate** switch.
- Click **Save Pipeline Schedule**.

---

### Key Limitations

- **Minimum Interval**: Scheduled pipelines cannot run more frequently than once every 60 minutes.
  - For example, you cannot configure a pipeline to run every 5 or 30 minutes. The smallest allowed interval is 1 hour.
- **Sequential Execution**: A new run will not start until 60 minutes have passed since the previous run.

---

### Managing Scheduled Pipelines

After creating a schedule, you’ll see an entry in the **Schedules** section. This includes:

1. **Pipeline Description**: A name to identify the schedule.
2. **Target Branch**: The branch the pipeline is associated with.
3. **Last Pipeline Run**: Displays the most recent run's ID (if applicable).
4. **Next Run**: Shows the time for the next scheduled execution.
   - GitLab calculates the next run based on the server’s time zone.
5. **Actions**:
   - **Play Button**: Manually trigger the pipeline if needed.
   - **Edit**: Modify the schedule settings.
   - **Delete**: Remove the schedule.

---

### Example: Creating a Daily Build Schedule

1. **Description**: "Daily Build"
2. **Cron Syntax**: `0 11 * * *` (Daily at 11 AM).
3. **Time Zone**: Set to your local time zone (e.g., EST).
4. **Target Branch**: `main`.
5. **Custom Variables**: Add any required variables like `BUILD_ENV=production`.

Once saved, this pipeline will execute automatically at 11 AM daily.

---

### Use Cases for Scheduled Pipelines

1. **Continuous Testing**:
   - Schedule daily tests to ensure code quality.

2. **Data Processing**:
   - Automate batch jobs or data pipelines.

3. **Periodic Deployments**:
   - Deploy applications weekly or during off-peak hours.

4. **Codebase Maintenance**:
   - Regularly check for outdated dependencies or vulnerabilities.

---

### Best Practices

1. **Validate Pipelines Before Scheduling**:
   - Use the [GitLab CI Lint Tool](#lint-tool) to check your `.gitlab-ci.yml` file for errors.
2. **Use Descriptive Names**:
   - Clearly name your schedules to avoid confusion, especially in large projects.
3. **Combine Schedules with Variables**:
   - Customize pipelines for different environments or conditions using variables.

---

### Final Thoughts

Scheduled pipelines in GitLab provide a powerful way to automate tasks at regular intervals. By understanding cron syntax and leveraging GitLab’s built-in scheduling tools, you can streamline workflows, maintain code quality, and reduce manual effort.

Take advantage of these features to optimize your CI/CD process. See you in the next class!