### GitLab Artifacts and Pipelines

#### Introduction

Hello everyone! In today’s lecture, we’ll dive into **artifacts** in GitLab. Along the way, we’ll also explore some key aspects of **GitLab pipelines**. Rather than overwhelming you with theory, we’ll start directly with an example to see artifacts in action. By the end of this lecture, you should have a better understanding of how artifacts work and gain additional insights into pipelines.

#### Step 1: Setting up the GitLab Project

To begin, I’ve already created a new GitLab project named **`artifacts_demo`**. (If you’re unsure how to create a new project, refer to the previous lectures or GitLab documentation.) 

Let’s clone this project to our local system:

```bash
git clone <repository_url>
```

Once cloned, switch to the **`dev` branch**:

```bash
git checkout dev
```

If we list the files using `ls`, we’ll notice the project is currently empty. To make things easier to visualize and edit, let’s open this project in **Visual Studio Code**.

#### Step 2: Creating a Basic Node.js Application

For this demonstration, we’ll create a simple **Node.js application**. If you’re familiar with Node.js, that’s great! For those who aren’t, here’s a brief overview:

**What is Node.js?**

- Node.js is an **open-source**, **cross-platform** runtime environment for running JavaScript code outside of a web browser.
- It’s widely used for developing **server-side** and **networking applications** that need persistent connections, such as real-time chat apps, news feeds, or web push notifications.

**What is npm?**

Node.js comes with a package manager called **npm (Node Package Manager)**. It allows developers to:
- Install third-party libraries, plugins, frameworks, or tools using simple commands.
- Manage dependencies for a project via a `package.json` file.

For instance, to install the **Express** framework, you’d run:

```bash
npm install express
```

For more in-depth details, feel free to check out the official documentation for [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/).

#### Step 3: Setting Up the Application Code

Let’s create a new file named `index.js` for our application. Since this is not a course on Node.js development, I’ll paste the code instead of writing it from scratch.

**Overview of the Code:**
1. **Express Framework**: This application uses **Express.js**, a lightweight web application framework for Node.js.
2. **Server Setup**:
   - The `express` module is imported into the `express` variable.
   - The `app` variable initializes the Express application.
   - A `port` variable is defined to specify the server port (8080).
3. **Routing**: The app listens for GET requests and responds with a message.
4. **Server Listening**: The server is started using the `listen` method, with a message indicating that the app is running.

Here’s the code:

```javascript
const express = require('express');
const app = express();
const port = 8080;

app.get('/', (req, res) => {
    res.send('Success! Your Node.js app is running.');
});

app.listen(port, () => {
    console.log(`App listening at http://localhost:${port}`);
});
```

#### Step 4: Managing Dependencies with `package.json`

Node.js applications rely on dependencies like **Express** to function. To manage these, we’ll create a `package.json` file. This file is essential for:
- Listing the project’s dependencies.
- Defining scripts for build and deployment.
- Providing metadata about the project.

Here’s a basic `package.json` file:

```json
{
  "name": "artifacts_demo",
  "version": "1.0.0",
  "description": "A simple Node.js application for demonstrating GitLab artifacts.",
  "main": "index.js",
  "dependencies": {
    "express": "^4.17.1"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```

Save both `index.js` and `package.json`. At this point, our application setup is complete.

#### Step 5: Running the Application Locally

Before moving to GitLab, let’s test the application locally. This step helps us identify any missing dependencies or commands that need to be included in our **`.gitlab-ci.yml`** pipeline file.

1. **Update System**:
   Always ensure your system is updated before installing new packages:
   ```bash
   sudo apt update
   ```

2. **Install Node.js and npm**:
   If Node.js isn’t installed, install it:
   ```bash
   sudo apt install nodejs npm
   ```

3. **Install Dependencies**:
   Run `npm install` to install the dependencies defined in `package.json`. The command automatically creates a `node_modules` directory with the required packages.

4. **Run the Application**:
   Start the application:
   ```bash
   node index.js
   ```

5. **Test the Application**:
   Open [http://localhost:8080](http://localhost:8080) in your browser. If everything is set up correctly, you’ll see the success message: *"Success! Your Node.js app is running."*

---

### Transitioning to GitLab

Now that the application is running locally, the next step is to:
1. Push the code to our GitLab repository.
2. Create a pipeline using a **`.gitlab-ci.yml`** file.

In the next lecture, we’ll:
- Write the **`.gitlab-ci.yml`** file.
- Specify the steps to build, test, and deploy the application.
- Learn how to save **artifacts** (e.g., build outputs) during the pipeline execution.

See you in the next class!