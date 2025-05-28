---

## 📌 Big Picture: What are we doing in the lab test?

👉 You’ll get a Java program
👉 You’ll turn it into a **Maven project**
👉 You’ll build it through **Jenkins** (Continuous Integration)
👉 You’ll use **Ansible** on **Ubuntu (inside WSL if you’re on Windows)** to move the output `.jar` file to a deploy folder

---

# 📒 COMPLETE STEP-BY-STEP (Based on your lab manual)

---

## 🔶 PART A: Java Project in Maven, Git, Jenkins CI

---

### ✅ Step 1: Create a Maven project

**In Windows Command Prompt / Terminal:**

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

✅ Go into the project:

```bash
cd demo
```

---

### ✅ Step 2: Replace Java file

Go to:

```
src/main/java/com/example/App.java
```

📝 Replace the code inside `App.java` with the Java program your teacher gives you during the test.
**OR**
Rename it to `HelloWorld.java` and add your program there if needed.

---

### ✅ Step 3: Initialize Git repository

**In Command Prompt:**

```bash
git init
git add .
git commit -m "Initial commit"
```

Then, create a **new repository on your GitHub account** called something like `program8` or `demo-project`.

Copy the repo URL.

Now, link your local project to GitHub:

```bash
git remote add origin https://github.com/YourUsername/demo-project.git
git branch -M main
git push -u origin main
```

✅ Now your project is on GitHub.

---

### ✅ Step 4: Run Jenkins

**In Command Prompt (Admin Mode)**
Go to where your `jenkins.war` is.

Example:

```bash
java -jar C:\Users\YourName\Downloads\jenkins.war
```

Then in your browser:

```
http://localhost:8080
```

Login using your username/password.

---

### ✅ Step 5: Create a Jenkins Job

* Click **New Item**
* Name: `demoCI`
* Type: **Pipeline**
* OK

Scroll down to **Pipeline Section**

* **Definition**: Pipeline Script
* Copy-paste this:

```groovy
pipeline {
    agent any
    tools {
        maven 'Apache Maven 3.9.9'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YourUsername/demo-project.git'
            }
        }
        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
        }
    }
}
```

✅ Save
✅ Click **Build Now**
✅ Check **Console Output** — should say `SUCCESS`

---

## 🔶 PART B: Use Ansible on Ubuntu to Deploy

---

**⚠️ If you’re on Windows, open your WSL Ubuntu terminal**

---

### ✅ Step 6: Create Ansible hosts.ini

In Ubuntu terminal:

```bash
vi hosts.ini
```

Type:

```
[local]
localhost ansible_connection=local
```

Save with `Esc` → `:wq`

---

### ✅ Step 7: Create Ansible Playbook deploy.yml

In Ubuntu terminal:

```bash
vi deploy.yml
```

Type:

```yaml
- name: Deploy artifact locally
  hosts: local
  tasks:
    - name: Copy JAR file
      copy:
        src: /mnt/c/Users/YourName/.jenkins/workspace/demoCI/target/demo-1.0-SNAPSHOT.jar
        dest: /mnt/c/Users/YourName/deployed/demo-1.0-SNAPSHOT.jar
```

✅ Replace `YourName` with your actual Windows username.

Save with `Esc` → `:wq`

---

### ✅ Step 8: Run Ansible Playbook

In Ubuntu:

```bash
ansible-playbook -i hosts.ini deploy.yml
```

---

### ✅ Step 9: Check if it worked

In Ubuntu:

```bash
ls /mnt/c/Users/YourName/deployed
```

✅ You should see:

```
demo-1.0-SNAPSHOT.jar
```

---

## 📌 Done ✅

**If teacher asks for demo, you can explain:**

* Maven compiles Java and builds a `.jar`
* Jenkins automates the build process from GitHub
* Ansible copies that `.jar` to a deploy folder (like a simple deployment)

---

## 📌 Extra Notes:

* `/mnt/c/` is how Ubuntu accesses your Windows drives.
* `vi` is a text editor in Ubuntu.

  * `i` to insert text
  * `Esc` → `:wq` to save and exit
* If Ansible isn’t installed:

```bash
sudo apt update
sudo apt install ansible
```

---
