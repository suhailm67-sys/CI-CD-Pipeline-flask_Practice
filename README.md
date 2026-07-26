# Graded Project on CI/CD Pipeline for Flask application using Jenkins
Objective: Set up a Jenkins pipeline that automates the testing and deployment of a simple Python web application.

## Part 1: Jenkins CI CD pipeline for flask application
### Step 1: Setup- Install Jenkins on a virtual machine or use a cloud-based Jenkins service.
1. Setup a EC2 instance
2. Install Jenkins in the EC2 instance and run it
```
sudo apt update
sudo apt install openjdk-17-jdk -y
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo apt install python3 python3-pip python3-venv -y
```
3. Access jenkins via http://<EC2-IP>:8080 - <img width="1885" height="827" alt="image" src="https://github.com/user-attachments/assets/c1fc80b1-d54f-4604-b5bd-72b5e5ea3fb7" />
4. Configure Jenkins with Python and any necessary libraries, plugins - `sudo apt install python3 python3-pip python3-venv git -y`
  - Git Plugin
  - GitHub Plugin
  - Pipeline
  - Email Extension Plugin
  - Workspace Cleanup
  - Build Timestamp
  - Blue Ocean (optional)

### Step 2: Source Code- Fork the provided Python web application repository on GitHub.
1. Fork the repo with - https://github.com/mohanDevOps-arch/flask_Practice.git - <img width="1666" height="371" alt="image" src="https://github.com/user-attachments/assets/471deef6-5a96-4acd-844b-ebd76c37d231" />
2. Clone the forked repository into your Jenkins server - `git clone https://github.com/suhailm67-sys/CI-CD-Pipeline-flask_Practice.git`

### Step 3: Jenkins Pipeline
1. Create a Jenkinsfile in the root of your Python application repository
2. Define a pipeline with the following stages:
  - Build: Install dependencies using pip.
  - Test: Run unit tests using a testing framework like pytest.
  - Deploy: If tests pass, deploy the application to a staging environment.
```
pipeline {

    agent any

    environment {
        VENV = "venv"
        MONGO_URI = "mongodb://172.18.0.1:27017/test_student_db"
        SECRET_KEY = "jenkins-secret"
    }

    stages {

        stage('Checkout') {

            steps {

                git branch: 'main',
                url: 'https://github.com/suhailm67-sys/CI-CD-Pipeline-flask_Practice.git'
            }
        }

        stage('Build') {

            steps {

                sh '''
                python3 -m venv $VENV
                . $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pip install pytest
                '''
            }

        }

        stage('Test') {

            steps {

                sh '''
                . $VENV/bin/activate
                pytest
                '''
            }

        }

        stage('Deploy') {

            steps {

                sh '''
                . $VENV/bin/activate
                nohup python app.py > flask.log 2>&1 &
                '''
            }

        }

    }

    post {

        success {

            emailext (
                subject: "SUCCESS: Jenkins Build ${BUILD_NUMBER}",
                body: "Build Successful",
                to: "suhailm67@gmail.com"
            )

        }

        failure {

            emailext (
                subject: "FAILED: Jenkins Build ${BUILD_NUMBER}",
                body: "Build Failed",
                to: "suhailm67@gmail.com"
            )

        }

    }

}
```
### Step 4: Triggers- Configure the pipeline to trigger a new build whenever changes are pushed to the main branch of the repository.
1. Create Jenkins Pipeline Job
2. Configure GitHub Webhook - <img width="1197" height="852" alt="image" src="https://github.com/user-attachments/assets/7254040e-d9c5-41b2-94ee-a875904724a5" />
3. Enable Jenkins Trigger - Pipeline >> Configure >> Build Triggers >> Enable `GitHub hook trigger for GITScm polling`

### Step 5: Notifications- Set up a notification system to alert via email when the build process fails or succeeds.
1. Configure Email Notification - Manage Jenkins >> Configure System >> Extended Email Notification and update the email there to receive the notification
  - SMTP server: smtp.gmail.com:587
  - Check Use TLS
  - Add your email credentials
  - Set Default Recipients
  - Update your-email@example.com in the Jenkinsfile with your actual email

## After running the build successfully the notfication is sent to email and this completes our Jenkins CI/CD Pipeline - 
<img width="1841" height="702" alt="image" src="https://github.com/user-attachments/assets/365fcbd2-cbac-4416-81a1-1feba9d6c2b5" />
<img width="1867" height="791" alt="image" src="https://github.com/user-attachments/assets/807b535c-9361-4405-9124-8243f790276c" />


# Student Registration System

A simple **Flask** web application to manage student records with **MongoDB** as the backend database. Users can **add, view, update, and delete** student details.

---

## Features

* List all students on the home page
* Add a new student
* Update existing student details
* Delete a student with confirmation
* Simple and responsive UI using Bootstrap

---

## Tech Stack

* **Backend:** Python, Flask
* **Database:** MongoDB (via Flask-PyMongo)
* **Frontend:** HTML, Jinja2 templates, Bootstrap 5
* **Environment Variables:** Managed via `.env` file

---

## Setup Instructions

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd <repo-folder>
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv
# Activate venv
# Windows:
venv\Scripts\activate
# Linux / Mac:
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

**`requirements.txt` example:**

```
Flask
Flask-PyMongo
python-dotenv
bson
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```
MONGO_URI=<your-mongodb-connection-string>
SECRET_KEY=<your-secret-key>
```

### 5. Run the application

```bash
python app.py
```

Open your browser at: [http://localhost:8000](http://localhost:8000)

---

## Project Structure

```
project/
│
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── add_student.html
│   ├── update_student.html
│
├── app.py
├── requirements.txt
└── .env
```

---

## Screenshots

**Home Page**
Lists all students with Edit/Delete buttons.
- <img width="1902" height="607" alt="image" src="https://github.com/user-attachments/assets/a58a6a6d-4978-4769-8074-232e4d31e69d" />


**Add Student**
Form to add a new student.
- <img width="1897" height="801" alt="image" src="https://github.com/user-attachments/assets/d65d25c3-ebb5-410a-adb1-e130ad7c5878" />


**Update Student**
Form pre-filled with student details.
- <img width="1905" height="897" alt="image" src="https://github.com/user-attachments/assets/04febf01-879f-431f-ab07-abcfb993acf1" />



---

## Notes

* Make sure MongoDB is running and accessible via the URI in `.env`
* Delete action includes a confirmation page to prevent accidental deletion
* Uses `ObjectId` from `bson` to work with MongoDB document IDs
* If you use MongoDB Atlas on macOS, install dependencies again (`pip install -r requirements.txt`). This project now uses `certifi` CA bundle explicitly to avoid common TLS certificate verification failures with `pymongo`.

---

## License

MIT License

---



