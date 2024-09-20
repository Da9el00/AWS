# Flask Hello World on Amazon EC2

This guide will help you set up and run a simple "Hello World" Flask web application on an Amazon EC2 instance.

## Prerequisites

Before getting started, make sure you have the following:

- An **Amazon EC2 instance** (Amazon Linux 2 or Ubuntu recommended)
- A **security group** with ports 22 (SSH) and 5000 (Flask) open
- **SSH access** to the EC2 instance (you will need your `.pem` key file)

---

## Steps to Deploy Flask on EC2

### 1. Launch an EC2 Instance

1. Go to the AWS Management Console and launch a new EC2 instance. You can use an **Amazon Linux 2** or **Ubuntu** AMI (Amazon Machine Image).
2. In the **Configure Security Group** section, ensure you add the following rules:
   - **Port 22**: For SSH access.
   - **Port 5000**: For accessing the Flask app from the browser.

### 2. Connect to the EC2 Instance

Once the EC2 instance is up and running, SSH into your instance with the following command:

```bash
ssh -i /path/to/your-key.pem ec2-user@<your-ec2-public-ip>
```

For Ubuntu, the default user might be `ubuntu`:

```bash
ssh -i /path/to/your-key.pem ubuntu@<your-ec2-public-ip>
```

### 3. Update the EC2 Instance

After connecting to your instance, update the system packages:

For **Amazon Linux**:

```bash
sudo yum update -y
```

For **Ubuntu**:

```bash
sudo apt update && sudo apt upgrade -y
```

### 4. Install Python and Pip

If Python and pip are not already installed, install them:

For **Amazon Linux 2**:

```bash
sudo yum install python3-pip -y
```

For **Ubuntu**:

```bash
sudo apt install python3-pip -y
```

### 5. Install Flask

Next, install Flask using pip3:

```bash
pip3 install Flask
```

### 6. Create the Flask Application

Create a directory for your Flask project and navigate into it:

```bash
mkdir flask-app
cd flask-app
```

Create a Python file (`app.py`) using a text editor, such as nano:

```bash
nano app.py
```

Then, paste the following code into the file:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

Save the file by pressing `Ctrl + O` (save as) or `Ctrl + S` (save), then exit the editor with `Ctrl + X` or use .

### 7. Run the Flask App

To start the Flask app, run:

```bash
python3 app.py
```

Flask will start a local web server on port 5000. You can now access your Flask app by visiting the EC2 instance's public IP in your web browser:

```
http://<your-ec2-public-ip>:5000
```

### 8. Keep the Flask App Running in the Background

To ensure Flask keeps running after you close the SSH session, use `nohup`:

```bash
nohup python3 app.py &
```
