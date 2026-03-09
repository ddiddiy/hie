# CC
## steps

### Prac 1
- Login

### Prac 2
#### EC2 Windows with RDP
- Create EC2 Windows instance
  - Launch a new instance
  - Choose Windows Server
  - Cerate a key pair and it will download a .pem file
  - Keep all the default settings and launch the instance
- Connect to the instance using RDP
  - Go to the EC2 dashboard and select the instance
  - Click on "Connect" and then "RDP client"
  - Download the RDP file and open it
  - Use the .pem file to get the password for the administrator account
  - Enter the password and connect to the instance

#### Amamzon Linux with SSH
- Create Amazon Linux instance
  - Launch a new instance
  - Choose Amazon Linux
  - Create a key pair and it will download a .pem file
  - Keep all the default settings and launch the instance  
- Connect to the instance using SSH
  - Go to the EC2 dashboard and select the instance
  - Click on "Connect" and then "SSH client"
  - Follow the instructions to connect using SSH
  - Locatate the .pem file and in the same folder a cmd 
    - run `icacls key.pem`
    - then run `the ssh cmd` given on the aws console
      - eg `ssh -i "LinuxDemo.pem" ec2-user@ec2-13-233-157-167.ap-south-1.compute.amazonaws.com`

#### Redhat Linux with SSH
- Create Redhat Linux instance
  - Launch a new instance
  - Choose Redhat Linux
  - Create a key pair and it will download a .pem file
  - Keep all the default settings and launch the instance
- Connect to the instance using SSH
  - Go to the EC2 dashboard and select the instance
  - Click on "Connect" and then "SSH client"
  - Follow the instructions to connect using SSH
  - Locatate the .pem file and in the same folder a cmd
    - run `icacls key.pem`
    - then run `the ssh cmd` given on the aws console
      - eg `ssh -i "RedhatDemo.pem" ec2-user@ec2-13-233-157-167.ap-south-1.compute.amazonaws.com`

### Prac 3
#### AWS Amplify
##### HTML
- Create a html file wiht basic code init
- zip the file with extension .zip
- in aws console, navigate to AWS Amplify and scroll down to create new app
- click on deploy without git provider
- click on next and then upload or drag and drop the .zip file
- click on save and deploy
- after deployment, click on the url to see the webpage

##### React
- Create a react app using `npx create-react-app my-app` or `npm create vite@latest`
- setup  the app and make sure it runs locally
- install react-router-dom using `npm install react-router-dom`
- then we gotta wirte some code to make a basic crud app
  - create three new file: `Home.jsx`, `Adduser.jsx`, `Users.jsx`
  - code for `App.jsx`:
```jsx
import { BrowserRouter, Route, Routes } from "react-router-dom";
import "./App.css";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users" element={<Users />} />
        <Route path="/add" element={<AddUser />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```
  - code for `Home.jsx`:
```jsx
import { Link } from "react-router-dom";

const Home = () => {
  return (
    <div>
      <h1>Home</h1>
      <Link to="/add">Add User</Link>
      <br />
      <Link to="/users">Users</Link>
    </div>
  );
};

export default Home;
```
  - code for `AddUser.jsx`:
```jsx
import { useState } from "react";

const AddUser = () => {
  const [name, setName] = useState("");

  const saveUser = async () => {
    await fetch("http://localhost:5000/users", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      }, 
      body: JSON.stringify({ name }),
    });
    alert("User added successfully")
    setName("")
  }

  return (
    <div>
      <h1>AddUser</h1>
      <input
        type="text"
        placeholder="Name"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <br />
      <button onClick={saveUser}>Save User</button>
    </div>
  );
};

export default AddUser;
```
  - code for `Users.jsx`:
```jsx
import React, { useEffect, useState } from 'react'

const Users = () => {
  
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("http://localhost:5000/users")
      .then((res) => res.json())
      .then((data) => setUsers(data));
  }, [])

  return (
    <div>
      <h1>Users</h1>
      {users.map((user) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  )
}

export default Users
```
  - then the app using `npm run dev`
  - then install json-server globally using `npm install -g json-server`
  - create a `db.json` file in the root folder with the following content:
```json
{
  "users": []
}
```
  - then run the json-server using `json-server --watch db.json --port 5000`
  - then just test out the app by adding some users and checking the users page


### Prac 4
#### Lambda function
- Go to AWS Lambda and click on "Create function"
- Choose "Author from scratch" and give the function a name
- Choose the runtime as "python 3.14"
- keep the default settings and click on "Create function"
- now we need to add some code to the function
```python
import json
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    length = event['length']
    width = event['width']
    area = calArea(length ,width)
    print(f"Area: {area}")
    logger.info(f"Cloudwatch logs group: {context.log_group_name}")
    data = {
        "area": area
    }
    return json.dumps(data)

def calArea(length, width):
    return length * width   
```
- deploy the function and then test it out by providing some input in the test event
- click on create a new test event and give it a name
- add some input in the event body like this:
```json
{
  "length": 5,
  "width": 10
}
```
### Prac 5
#### VPC and Subnet
- 1 VPC
- 2 Subnets (1 public and 1 private)
- 2 Route tables (1 for public and 1 for private)
- 1 Internet Gateway for public 
- 1 NAT Gateway for private
- 2 Security Groups (1 for public and 1 for private)
- 1 Key Pair for SSH access to the instances
- Roles - AWS S3
- 1 EC2 instance in public subnet and 1 EC2 instance in private subnet

#### steps
- Create a VPC
  - Go to the VPC dashboard and click on "Create VPC"
  - Give the VPC a name and a CIDR block - 10.0.0.0/16
  - Click on "Create VPC"

- Create Subnets
  - Public Subnet
    - Go to the Subnets dashboard and click on "Create subnet"
    - Give the subnet a name and select the VPC we just created
    - Choose an availability zone and give the subnet a CIDR block - 10.0.1.0/24
    - Click on "Create subnet"
  - Private Subnet
    - Go to the Subnets dashboard and click on "Create subnet"
    - Give the subnet a name and select the VPC we just created
    - Choose an availability zone and give the subnet a CIDR block - 10.0.0.0/24
    - Click on "Create subnet"

- Create Route Tables
  - Public Route Table
    - Go to the Route Tables dashboard and click on "Create route table"
    - Name the route table and select the VPC we just created
    - Click on "Create route table"
    - then right click on the route table and click on "Edit Subnet associations"
    - select the public subnet and click on "Save"
  - Private Route Table
    - Go to the Route Tables dashboard and click on "Create route table"
    - Name the route table and select the VPC we just created
    - Click on "Create route table"
    - then right click on the route table and click on "Edit Subnet associations"
    - select the private subnet and click on "Save"

- Create Internet Gateway
  - Go to the Internet Gateways dashboard and click on "Create internet gateway"
  - right click on the internet gateway and click on "Attach to VPC"
  - select the VPC we just created and click on "Attach internet gateway"

- Create NAT Gateway
  - Go to the NAT Gateways dashboard and click on "Create NAT gateway" 
  - Select VPC which we just created and availability region 
  - other things will be default, click on "Create NAT gateway"

- Security Groups
  - Public Security Group
    - Go to the Security Groups dashboard and click on "Create security group"
    - for now just eneter the name and description and select the VPC we just created
    - we will update the inbound and outbound rules later
    - Click on "Create security group"    
  - Private Security Group
    - Go to the Security Groups dashboard and click on "Create security group"
    - for now just eneter the name and description and select the VPC we just created
    - we will update the inbound and outbound rules later
    - Click on "Create security group"

- Create Key Pair
  - Go to the Key Pairs dashboard and click on "Create key pair"
  - Give the key pair a name and select the key pair type as "RSA"
  - Select the private key format as "pem" and click on "Create key pair"
  - The .pem file will be downloaded, keep it safe as we will need it to connect to the instances

- Create Roles
  - Search for IAM in the AWS console and go to the IAM dashboard
  - Click on "Roles" and then "Create role"
  - Choose the use case as "AWS service" and then select "EC2" as the service that will use this role and click on "Next: Permissions"
  - search for `AmazonS3FullAccess` and select it and click on "Next: Tags"
  - add any tags if you want and click on "Next: Review"
  - in review tab dont change anything and just give the role a name `S2-Full-Service` and click on "Create role"

- Create EC2 instances
  - Public EC2 instance
    - Go to the EC2 dashboard and click on "Launch instance"
    - Give any name to the instance and select the Ubuntu Server 22.04 LTS (HVM) AMI
    - key pair - select the key pair we just created
    - for network
      - first we have to edit it and select the VPC we just created
      - then for subnet, select the public subnet we just created
      - for auto-assign public IP, select "Enable"
    - for security group, select the public security group we just created
    - keep the default settings and click on "Launch instance"
  - Private EC2 instance
    - Go to the EC2 dashboard and click on "Launch instance"
    - Give any name to the instance and select the Ubuntu Server 22.04 LTS (HVM) AMI
    - key pair - select the key pair we just created
    - for network
      - first we have to edit it and select the VPC we just created
      - then for subnet, select the private subnet we just created
      - for auto-assign public IP, select "Enable"
    - for security group, select the private security group we just created
    - keep the default settings and click on "Launch instance"

- now navigate to route tables and right click on the public route table and click on "Edit routes"
  - click on "Add route" and for destination enter `0.0.0.0/0` and for target select the internet gateway we just created and click on "Save routes"
- now again navigate to route tables and right click on the private route table and click "Edit Routes"
  - CLick on "add  routes" and for destination enter `0.0.0.0/0` and target select the NAT gateway we just created and click on "Save routes"

- Security Group Rules
  - Public Security Group
    - Inbound rules
      - Type: SSH, Protocol: TCP, Port Range: 22, Source: My IP, it will automatically detect your IP address and select it if not then go to `checkmyip.amazonaws.com` and check your IP address and enter it here with /32 at the end
  - Private Security Group
    - Inbound rules
      - Type: SSh, Protocol: TCP, Port Range: 22, Source: Custom, here the ip should be the private IP address of the public EC2 instance with /32 at the end, you can find the private IP address of the public EC2 instance in the EC2 dashboard by selecting the instance and looking at the details section

- SSH
- first in cmd where key is there run `icacls key.pem` to check the permissions of the key file
- then paste the ssh command given in the AWS console for the public EC2 instance and run it in cmd to connect to the public EC2 instance
- once we are connected to the public EC2 instance, run `sudo apt update` 
- then we have to get the pem file in the puvlic EC2 instance to connect to the private EC2 instance, for that we can use `scp` command in cmd `scp -i "C:\Users\LENOVO\Downloads\vpc-demo.pem" "C:\Users\LENOVO\Downloads\vpc-demo.pem" ubuntu@15.206.79.85:/home/ubuntu/`
- once this is done on the public EC2 instance we can check using `ls`
- now we can connect to the private EC2 instance using the ssh command given in the AWS console for the private EC2 instance and running it in the public EC2 instance terminal
- but before tht we have to run `chmod 400 vpc-demo.pem` to change the permissions of the pem file in the public EC2 instance
- then we ssh into private EC2 instance using `ssh -i "vpc-demo.pem" ubuntu@<private-ip-address-public-ec2>` and we should be able to connect to the private EC2 instance


### Prac 6
#### Jenkins
- locate the jenkins.war file and run it using `java -jar jenkins.war --httpPort=9090`
- open the browser and go to `http://localhost:9090`
- then we have to paste the admin password which willbe in the terminal where we ran the jenkins.war file
- then we have to install the suggested plugins and create an admin user
- after which create new admin with username, password, email and name
- after which we will land on the jenkins dashboard where we can create new jobs and pipelines
- we make a new users for which we ahve to navigate to settings > secruity > users > create new user 
- we made 2 users
- then we navigate to settings > secruity > Authorize > Matrix-based security and give the users the required permissions
- ![alt text](image.png)
- now if other user tries to access the jenkins dashboard, they will only see the jobs and pipelines that they have access to and wont be able to see the other jobs and pipelines
- now we make a new pipeline as the admin user
  - navigate to new item and give freestyle project and give it a name
  - add a desc
  - now in build steps add windows batch command and add the following command:
  ```bash
  python --version
  ```
  - then save and build the project and check the console output to see the python version
  - once we verify its working we can add a python file to the project
  - navigate to `user/.jenkins/workspace/ProjectName` and paste the python file there
  - then on the jankins dashboard, navigate to the project and workspace and add the python file to the project
  - then in the build steps, change the command to run the python file:
  ```bash
  python filename.py
  ```
  - then save and build the project and check the console output to see the output of the python file 

### Prac 7
#### Apache Tomcat with Jenkins
- Download the apache tomcat server and extract it to a folder into c drive
- we need to update some cofiguration files in the apache tomcat server here as follows:
  - navigate to `conf/server.xml` and change the port number from 8080 to 9091
  ```xml
  <Connector port="9091" protocol="HTTP/1.1"
            connectionTimeout="20000"
            redirectPort="8443" />
  ```
  - we also need amazon corretto java to run the apache tomcat server so we need to set the environment variable for java home
  - to run the apache tomcat server, navigate to the bin folder and run the startup.bat file in cmd 
  - after which again run jenkins server using `java -jar jenkins.war --httpPort=9090`
- now we need to create a new pipeline in jenkins to deploy a web application on the
- click on new item and give it a name and select pipeline and click on ok
- add a desc and pipline script as follows:
```groovy
node {
    stage('DEV')
    stage("QA")
    {
        input 'QA Deply?'
    }
    stage('deploy to prod')
    {
        bat 'xcopy C:\\Users\\LENOVO\\Desktop\\sem6-praccs\\index.html C:\\apache-tomcat-11.0.18\\webapps\\ROOT /E /I /Y' 
    }
}
```
- after which save and build the project and check the console output to see the deployment process
- after the build is successful, we can check the deployment by going to `http://localhost:9091` and we should see the index.html file that we deployed

### Prac 8
#### Ansible with AWS

- make a EC2 instance with Ubuntu Server 22.04 LTS (HVM) AMI and with it create a new key pair
- in cmd  `icacls key.pem` to check the permissions of the key file
- then run the ssh command given in the AWS console for the EC2 instance and run it
- then `sudo su` and then `apt update` to update the instance
- then `sudo apt update --allow-insecure-repositories` to allow insecure repositories
- then `sudo apt install ansible -y` to install ansible on the instance
- then `ansible --version` to check the ansible version
- then `nano install_nginx.yml` to create a new ansible playbook file
- then add the following code to the playbook file:
```yaml
---

- name : Install and Start Nginx 
  hosts: localhost
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Start Nginx
      service: 
        name: nginx
        state: started
        enabled: yes

    - name: Create file
      file:
        path: /home/ubuntu/demo.txt
        state: touch
```
- then save and exit the file
- then run the playbook using `ansible-playbook -i hosts install_nginx.yml` and check the output to see if the playbook ran successfully
- we can also check if the file is created by running `ls` in the home directory and we should see the demo.txt file there
- another way to verify is `sudo systemctl status nginx`
- another way to verify is `curl localhost` and we should see the nginx welcome page
- to check the nginx server on our browser, we need make some changes to ec2'
  - navigate to the EC2 dashboard and select the instance
  - click on "Actions" and then "Security" and then "Modify security groups"
  - then select the security group and click on "Edit inbound rules"
  - then click on "Add rule" and select "HTTP" as the type, "TCP" as the protocol, "80" as the port range and Source 0.0.0.0/0
  - then click on "Save rules"
  - copy the public IP address of the instance and paste it in the browser and we should see the nginx welcome page
