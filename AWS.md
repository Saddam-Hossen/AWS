
---

<details>
  <summary><strong>Policy</strong></summary>

### ✅ 1. **Policy**

* A **policy** is a JSON document that **defines permissions**.
* It specifies **who can do what on which resources** (Allow/Deny).

**Example:**

```json
{
  "Effect": "Allow",
  "Action": "s3:ListBucket",
  "Resource": "arn:aws:s3:::example-bucket"
}
```

**Types:**

* **Identity-based policies**: Attached to users, groups, or roles.
* **Resource-based policies**: Attached directly to AWS resources (like S3 buckets, Lambda functions).
* **Permission boundaries**: Limits what a user or role can do even if their policies allow more.

</details>
---

<details>
  <summary><strong>Service</strong></summary>
  
### ✅ 2. **Service**

* An **AWS service** is a feature or capability offered by AWS (e.g., EC2, S3, Lambda).
* IAM **controls access** to these services through **policies**.

  </details>
---
<details>
  <summary><strong>Role</strong></summary>
  
### ✅ 3. **Role**

* A **role** is an AWS identity with permissions but **not associated with a specific user**.
* Used by **AWS services, applications, or users** from **another AWS account or identity provider (like SSO)**.
* Example Use Cases:

  * EC2 instance assumes a role to access S3.
  * Lambda function assumes a role to read from DynamoDB.
  * Cross-account access via roles.
</details>
---

<details>
  <summary><strong>Users</strong></summary>
### ✅ 4. **User**

* An **IAM user** represents a **person or application** with **long-term credentials** (username, password, access keys).
* Each user can be:

  * Assigned individual policies
  * Added to groups
  * Use access keys for programmatic access
</details>
---
<details>
  <summary><strong>Group</strong></summary>
### ✅ 5. **Group**

* A **group** is a **collection of IAM users**.
* You can attach **policies to the group**, and all users in that group will inherit the permissions.
* Helps manage **permissions efficiently**.
</details>
---

<details>
  <summary><strong>Permission Boundary</strong></summary>
 Great question — let’s clarify it **simply and precisely**:

---

## 🔒 **Permission Boundary: Means "He *Can* Do This, But Only If Also Allowed"**

### 🧠 Think of it as:

> A **limit** or **fence** — the **maximum** a user or role is allowed to do.
> It **does not grant permissions by itself**, but it says:
>
> > ✅ "You are *allowed* to do these actions...
> > ❌ You are *never* allowed to do anything beyond this."

---

## ✅ So What Does It Mean?

| If...                                         | Then...                                              |
| --------------------------------------------- | ---------------------------------------------------- |
| The **permissions policy allows** an action   | ✅ Only allowed if it's also in the boundary          |
| The **permissions boundary allows** an action | ❌ Not enough — needs to be in permissions policy too |
| The action is **missing from either**         | ❌ Denied                                             |

---

## 🔄 Summary Rule:

> ✅ **Effective permissions = (permissions policy) ∩ (permissions boundary)**
> (Only actions allowed by both)

---

## 🔧 Example:

### You have:

* **Permissions policy:**
  Allows `s3:GetObject` (read from S3)

* **Permissions boundary:**
  Allows `s3:PutObject` (write to S3)

### 🔍 What happens?

| Action         | Allowed? | Why?                                            |
| -------------- | -------- | ----------------------------------------------- |
| `s3:GetObject` | ❌ No     | Not allowed in boundary                         |
| `s3:PutObject` | ❌ No     | Not allowed in permission policy                |
| Any S3 action  | ❌ No     | There is no overlap between policy and boundary |

---

## 🛡️ In short:

> 🔹 **Permissions policy** says: “What the user is *supposed* to do”
> 🔹 **Permission boundary** says: “What the user is *allowed at most* to do”
> 🔐 ✅ The **user can only do** what is allowed by *both*





#✅ **If you do not set a permissions boundary**, and you only attach a **permissions policy**, then:

---

### ✅ The user or role **will have all the permissions** defined in the policy (as long as no other global restrictions apply).

### 🔍 In other words:

> **Permissions boundary is optional.**
> If it's not set, the **permissions policy alone** determines what the IAM user/role **can and cannot do**.

---

## 🔄 Example:

### You create a user `john_user` with this policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Resource": "*"
    }
  ]
}
```

#### And you **do not set any permission boundary**.

### ✅ Then:

* `john_user` **can list S3 buckets**
* `john_user` **can get S3 objects**
* ❌ Cannot do anything else (unless granted by other attached policies)

---

## 🚫 When is a boundary *required*?

A **permissions boundary is only required** in specific scenarios like:

* When an **admin sets a policy saying all new users must have boundaries**
* When using **service control policies (SCPs)** or **delegated IAM role creation** to restrict what roles can be created

---

## ✅ Summary:

| Situation                      | Result                                 |
| ------------------------------ | -------------------------------------- |
| Only policy is set             | ✅ Full access as defined by the policy |
| Policy + boundary set          | ✅ Only access allowed by **both**      |
| Policy allows, boundary denies | ❌ Denied                               |
| Policy denies, boundary allows | ❌ Denied                               |
| No policy at all               | ❌ No access                            |

---




</details>
---

<details>
  <summary><strong>Resources</strong></summary>
  Great question!

In AWS IAM, **"Resources"** refers to the **specific AWS objects or services** that your IAM **policy is allowing or denying access to**.

---

## ✅ **Definition:**

> A **resource** in IAM is the **object or entity** on which an action is performed.

For example:

* An **S3 bucket**
* An **EC2 instance**
* A **DynamoDB table**
* A **Lambda function**
* A **KMS key**
* A **VPC**

---

## 🔍 Example: Policy with Resource

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket-name/*"
}
```

### 🔸 Meaning:

* `Action`: The action is `s3:GetObject` (read file)
* `Resource`: Only applies to objects **in the bucket** `my-bucket-name`

✅ So the user can read files in `my-bucket-name`, but **nowhere else**.

---

## 🧠 Resource in ARN Format

Most resources are written using **ARNs** (Amazon Resource Names). The general format is:

```
arn:aws:<service>:<region>:<account-id>:<resource-type>/<resource-name>
```

### 🧾 Examples:

| AWS Service | Resource ARN Example                                              |
| ----------- | ----------------------------------------------------------------- |
| S3          | `arn:aws:s3:::my-bucket/*`                                        |
| EC2         | `arn:aws:ec2:us-east-1:123456789012:instance/i-1234567890abcdef0` |
| Lambda      | `arn:aws:lambda:us-east-1:123456789012:function:my-function`      |
| DynamoDB    | `arn:aws:dynamodb:us-west-2:123456789012:table/my-table`          |

---

## 🔐 Special Values

* `"Resource": "*"`
  ⟶ All resources (be careful! This means global access to everything the action applies to)

* `"Resource": "arn:aws:s3:::my-bucket-name/*"`
  ⟶ Only files inside `my-bucket-name`

* `"Resource": "arn:aws:lambda:*:*:function:*"`
  ⟶ All Lambda functions in all regions

---

## 🔄 Common Pattern in Policies:

```json
{
  "Effect": "Allow",
  "Action": "ec2:StartInstances",
  "Resource": "*"
}
```

🟢 Meaning: The user is allowed to start **any EC2 instance** in the account (no restriction).

---

### ✅ Summary:

| Term         | Means...                                                 |
| ------------ | -------------------------------------------------------- |
| **Action**   | What the user can do (e.g., `s3:GetObject`)              |
| **Resource** | What the action can be done **on** (e.g., `my-bucket/*`) |
| **Effect**   | `Allow` or `Deny`                                        |


  
  
  </details>
---
<details>
  <summary><strong>Service VS Resources</strong></summary>
  Excellent! Let's break down the difference between **Resources** and **Services** in AWS — this is a key concept in IAM and understanding permissions.

---

## ✅ 1. **Service**:

A **service** is a product or offering provided by AWS.

### 🔸 Examples of Services:

| Service Name | What It Does                           |
| ------------ | -------------------------------------- |
| **S3**       | Object storage (buckets, files)        |
| **EC2**      | Virtual servers (instances)            |
| **DynamoDB** | NoSQL database                         |
| **Lambda**   | Serverless function execution          |
| **RDS**      | Managed relational databases           |
| **IAM**      | Access control and identity management |

---

## ✅ 2. **Resource**:

A **resource** is a **specific object inside a service** that you act on.

### 🔸 Examples of Resources by Service:

| Service | Resource Example                                          |
| ------- | --------------------------------------------------------- |
| S3      | A bucket (`my-bucket`), an object (`my-bucket/image.jpg`) |
| EC2     | An instance (`i-1234567890abcdef0`)                       |
| Lambda  | A function (`my-function`)                                |
| RDS     | A database instance (`my-db-instance`)                    |
| IAM     | A user (`JohnDoe`), a role (`LambdaExecutionRole`)        |

---

## 📌 Key Difference:

| Concept      | Definition                             | Example                             |
| ------------ | -------------------------------------- | ----------------------------------- |
| **Service**  | An AWS product offering                | `s3`, `ec2`, `lambda`, `rds`, `iam` |
| **Resource** | A specific entity **within** a service | `arn:aws:s3:::my-bucket/image.jpg`  |

---

## 🎯 IAM Policy Example

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

* **Service:** `s3`
* **Action:** `GetObject` (a service-specific action)
* **Resource:** All files inside the bucket `my-bucket`

---

## 🔄 Another Example

```json
{
  "Effect": "Allow",
  "Action": "ec2:StartInstances",
  "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/i-abcdef1234567890"
}
```

* **Service:** `ec2`
* **Resource:** a specific EC2 instance

---

## ✅ Summary Table

| Term         | What it refers to                     | Example                            |
| ------------ | ------------------------------------- | ---------------------------------- |
| **Service**  | The AWS product you're working with   | `S3`, `EC2`, `Lambda`, `RDS`       |
| **Resource** | A specific entity inside that service | `arn:aws:s3:::my-bucket/image.jpg` |

  </details>
---
<details>
  <summary><strong>Amazon Resource Name (ARN)</strong></summary>
  Excellent! 🎯 When creating an **IAM policy** in AWS, you can set a **specific resource** using its **ARN (Amazon Resource Name)** in the `"Resource"` field of your JSON policy.

---

## ✅ Steps to Set a Specific Resource in an IAM Policy

### 🔸 1. Go to **IAM > Policies > Create Policy**

### 🔸 2. Choose the **JSON** tab

### 🔸 3. Write a policy like:

### 💡 Example: Allow read access to **one specific S3 bucket**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-specific-bucket",         // Bucket itself
        "arn:aws:s3:::my-specific-bucket/*"        // All objects inside the bucket
      ]
    }
  ]
}
```

---

## 🧠 How to Find the ARN Format?

Each AWS service has a different ARN format.

### ✅ Common ARN Formats:

| Service | ARN Format                                                 |
| ------- | ---------------------------------------------------------- |
| S3      | `arn:aws:s3:::bucket-name` or `arn:aws:s3:::bucket-name/*` |
| EC2     | `arn:aws:ec2:region:account-id:instance/instance-id`       |
| Lambda  | `arn:aws:lambda:region:account-id:function:function-name`  |
| RDS     | `arn:aws:rds:region:account-id:db:db-instance-name`        |

---

### 💡 Example: Allow start/stop for **one EC2 instance**

```json
{
  "Effect": "Allow",
  "Action": [
    "ec2:StartInstances",
    "ec2:StopInstances"
  ],
  "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/i-0123456789abcdef0"
}
```

---

## 📍 Tips:

* You can use the **AWS Policy Generator**:
  👉 [https://awspolicygen.s3.amazonaws.com/policygen.html](https://awspolicygen.s3.amazonaws.com/policygen.html)
  Choose "Specific" resource to generate ARNs.

* Use `"Resource": "*"` only when:

  * You **must** (e.g., `iam:CreateUser` requires `*`)
  * You're okay with applying actions to **all resources**

---

## ✅ Summary:

| Field        | Purpose                         | Example                                  |
| ------------ | ------------------------------- | ---------------------------------------- |
| `"Action"`   | What you want to allow/deny     | `"s3:GetObject"`, `"ec2:StartInstances"` |
| `"Resource"` | Where you want to allow/deny it | `"arn:aws:s3:::my-bucket"`               |


  Great question! Unlike services like S3 or EC2, **IAM is a global service**, and some of its actions **require special handling in the `Resource` field**, especially when referencing IAM users, roles, or policies.

---

## ✅ How to Add an ARN for IAM Resources

### 🧠 General ARN Format for IAM:

```
arn:aws:iam::<account-id>:<resource-type>/<resource-name>
```

---

## 🔸 Common IAM ARN Examples

| Resource Type        | ARN Format Example                                             |
| -------------------- | -------------------------------------------------------------- |
| **User**             | `arn:aws:iam::123456789012:user/JohnDoe`                       |
| **Role**             | `arn:aws:iam::123456789012:role/MyRole`                        |
| **Policy**           | `arn:aws:iam::123456789012:policy/MyCustomPolicy`              |
| **Group**            | `arn:aws:iam::123456789012:group/Developers`                   |
| **Instance profile** | `arn:aws:iam::123456789012:instance-profile/MyInstanceProfile` |

---

## 🔧 Example: Allow Managing One Specific IAM User

```json
{
  "Effect": "Allow",
  "Action": [
    "iam:GetUser",
    "iam:UpdateUser",
    "iam:DeleteUser"
  ],
  "Resource": "arn:aws:iam::123456789012:user/JohnDoe"
}
```

> 🔹 This policy allows managing **only the IAM user named `JohnDoe`** in account `123456789012`.

---

## ⚠️ Important Notes for IAM ARNs

* IAM is **global**, so **region is not used** in IAM ARNs.
* Some IAM actions **must use `"Resource": "*"`** because they don’t support specific ARNs (e.g., `iam:CreateUser`, `iam:ListUsers`).
* You can still restrict those actions using **conditions**.

---

## 🔒 Example with Conditions (for CreateUser)

```json
{
  "Effect": "Allow",
  "Action": "iam:CreateUser",
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "iam:RequestedUserName": "NewDevUser"
    }
  }
}
```

> ✅ This allows creating a user named **only `NewDevUser`**, even though `"Resource": "*"` is required.

---

## ✅ Summary

| IAM Resource | Example ARN                                 |
| ------------ | ------------------------------------------- |
| User         | `arn:aws:iam::123456789012:user/Alice`      |
| Role         | `arn:aws:iam::123456789012:role/AdminRole`  |
| Policy       | `arn:aws:iam::123456789012:policy/MyPolicy` |

---


  
  
  </details>
---

<details>
  <summary><strong>EC2 Policy Json</strong></summary>
    
  {
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "VisualEditor0",
			"Effect": "Allow",
			"Action": [
				"ec2:*",
				"ec2-instance-connect:*",
				"autoscaling:*",
				"imagebuilder:*",
				"elasticloadbalancing:*"
			],
			"Resource": "*"
		}
	]
}
![image](https://github.com/user-attachments/assets/ca9aaae7-f808-44c8-a5a8-c6a85c044fa5)


Great — let's analyze your IAM policy carefully.

---

## 🧾 IAM Policy You Provided:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "ec2:*",
        "ec2-instance-connect:*",
        "autoscaling:*",
        "imagebuilder:*",
        "elasticloadbalancing:*"
      ],
      "Resource": [
        "arn:aws:ec2:eu-north-1:661062380194:volume/vol-044158414107edabc"
      ]
    }
  ]
}
```

---

## ✅ Explanation (Plain English):

### 🔐 **Effect**: `"Allow"`

This statement is trying to allow access.

### 🔧 **Actions**:

The policy allows **everything** under:

* `ec2:*` → EC2 actions (like StartInstances, AttachVolume, TerminateInstances, etc.)
* `ec2-instance-connect:*` → Browser-based EC2 Connect
* `autoscaling:*` → Auto Scaling Group management
* `imagebuilder:*` → EC2 Image Builder actions
* `elasticloadbalancing:*` → Load Balancer management

### 📍 **Resource**:

The access is limited to **only one specific EBS volume**:

```
arn:aws:ec2:eu-north-1:661062380194:volume/vol-044158414107edabc
```

---

## 🚫 What This Means in Practice:

Even though the **actions** list is broad (`ec2:*`, etc.), they will **only apply to one EBS volume**.

### ✅ What the user **can do**:

* The user **can perform actions on this specific volume** (`vol-044158414107edabc`) — assuming those actions support resource-level control (e.g., `ec2:AttachVolume`, `ec2:DeleteVolume`, etc.)

### ❌ What the user **cannot do**:

* They **cannot** start/stop EC2 instances (because EC2 instances are **not** this volume)
* They **cannot** manage Auto Scaling, Load Balancers, or EC2 Image Builder resources — because those services' resources are **not the volume**
* They **cannot** use EC2 Instance Connect — it applies to **instances**, not volumes

---

## 🛑 Why Most Actions Will Fail:

Most of the actions you listed apply to **other resource types** like:

* EC2 instances (`instance/i-...`)
* Load balancers (`arn:aws:elasticloadbalancing:...`)
* Auto Scaling groups (`arn:aws:autoscaling:...`)
* Not to EBS volumes

Since your `"Resource"` only includes **one EBS volume**, actions on **any other EC2 resource** will be denied.

---

## ✅ Conclusion

### 🔍 Effective Policy Summary:

| Action Type                          | Will Work? | Why?                                              |
| ------------------------------------ | ---------- | ------------------------------------------------- |
| `ec2:AttachVolume` (for this volume) | ✅ Yes      | Action applies to volume and the volume is listed |
| `ec2:StartInstances`                 | ❌ No       | The instance ARN is not listed in `"Resource"`    |
| `ec2-instance-connect:*`             | ❌ No       | Applies to instances, not volume                  |
| `autoscaling:*`                      | ❌ No       | Applies to ASGs, not volume                       |
| `elasticloadbalancing:*`             | ❌ No       | Applies to load balancers, not volume             |

---

## ✅ Recommendation:

If your intent is to:

* Only allow volume actions → you're doing it correctly (but remove unrelated services)
* Allow full access to EC2 + volume → add `"Resource": "*"` or include proper ARNs for instances/load balancers etc.

---

Would you like me to:

* Optimize this policy for **volume-only access**
* Or rewrite it to allow full EC2 control?




  </details>

---
<details>
  <summary><strong>Setup AWS For Project Deploy</strong></summary>
	<details>
             <summary><strong>Install java</strong></summary>
		Great! You want to use **JDK 21**. Let's update everything accordingly for your **EC2 Ubuntu 24.04 Jenkins server**.

---

## ✅  EC2 Setup to Use Java 21 (instead of Java 17)

---

### 🔧 Step 2: Install Java 21

```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
```

Check version:

```bash
java -version
```

Expected:

```bash
openjdk 21.0.x ...
```

---

### 🔧 Step 3: Set Java 21 as Default (if needed)

```bash
sudo update-alternatives --config java
# Choose the Java 21 path if multiple installed
```

---

## ✅ Update Dockerfile to Use JDK 21

In your Spring Boot project, update the `Dockerfile` to use a JDK 21 base image:

```Dockerfile
# Dockerfile
FROM eclipse-temurin:21-jdk-alpine
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

> `eclipse-temurin` is the official build from Adoptium — stable and widely used.

---

## ✅ Update Jenkinsfile (no changes needed for Java 21)

As long as **your EC2 has Java 21** and **Dockerfile uses JDK 21**, no Jenkinsfile changes are needed.

---

## ✅ Summary of Changes

| Component     | Change                               |
| ------------- | ------------------------------------ |
| EC2 Ubuntu    | Installed OpenJDK 21                 |
| Dockerfile    | Switched base image to JDK 21        |
| Jenkins Build | Inherits system JDK (now version 21) |

---

### ✅ Next Steps

Let me know if you want me to now generate:

* A complete working **`Dockerfile`**
* A customized **`Jenkinsfile`**
* A **`deployment.yaml`** to deploy to EKS


	</details>
 
 ---
<details>
  <summary><strong>Install jenkins</strong></summary>
	Here's a **step-by-step guide to install Jenkins** on your **Ubuntu 24.04 EC2 instance** using bash commands — clean and copy-paste ready.

---

## ✅ Jenkins Installation on Ubuntu 24.04

### 📌 Step 1: Add Jenkins GPG key and repository

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

---

### 📌 Step 2: Update package list

```bash
sudo apt update
```

---

### 📌 Step 3: Install Jenkins

```bash
sudo apt install -y jenkins
```

---

### 📌 Step 4: Start and enable Jenkins

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

---

### 📌 Step 5: Allow Jenkins port through the firewall (optional, if UFW is active)

```bash
sudo ufw allow 8080
```

---

### 📌 Step 6: Check Jenkins status

```bash
systemctl status jenkins
```

You should see:

```
Active: active (running)
```

---

### 📌 Step 7: Access Jenkins in your browser

Open this in browser:

```text
http://<your-ec2-public-ip>:8080
```

Get the unlock password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## ✅ After Jenkins Starts

1. Paste the password shown above
2. Install **Suggested Plugins**
3. Create **Admin User**
4. Done! 🎉 Jenkins is ready to use.

---

Would you like help setting up:

* Maven build
* Docker build
* Jenkinsfile
* GitHub integration?


</details>

---
<details>
  <summary><strong>Insatall Docker and Buildx</strong></summary>
	# Install-docker-with-buildx-for-multi-platform-builds

It looks like you're manually adding the Docker repository to your **Ubuntu 24.04.2 LTS** system. If you're setting up Docker, follow these steps to ensure everything is configured correctly:

### **Step 1: Install Required Dependencies**
Before proceeding, install necessary dependencies:
```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
```

### **Step 2: Add Docker’s Official GPG Key**
You've already run this command, but to confirm:
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo tee /etc/apt/keyrings/docker.asc > /dev/null
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### **Step 3: Add the Docker Repository**
You've correctly set up the repository:
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### **Step 4: Update Package List**
```bash
sudo apt update
```

### **Step 5: Install Docker and Buildx**
Now, install Docker, including **Buildx** and **Compose**:
```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### **Step 6: Start and Enable Docker**
```bash
sudo systemctl enable --now docker
```

### **Step 7: Verify Installation**
Check if Docker and Buildx are installed:
```bash
docker --version
docker buildx version
```




saddamnvn@DESKTOP-PTTGD06:~$ docker ps -a
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.49/containers/json?all=1": dial unix /var/run/docker.sock: connect: permission denied

```
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock
```

means your current user (`saddamnvn`) doesn't have permission to access the Docker daemon.

---

### ✅ Fix: Add your user to the `docker` group

Run the following command:

```bash
sudo usermod -aG docker $USER
```

Then **log out and log back in** (or reboot) to apply the group changes.

> 💡 Alternatively, you can run `newgrp docker` in the same session to apply it immediately without rebooting.

---

### 🔁 Then try again:
```bash
docker info
```

It should now show the full Docker server info without any permission errors.

---


</details>
---
<details>
  <summary><strong> Attach IAM Role to EC2 (Best Practice for Production)</strong></summary>
   Yes — even if you SSH into your EC2 instance using the **root `.pem` key**, you still **need to configure the AWS CLI manually** by running:

```bash
aws configure
```

### 🔍 Why?

The `.pem` key only gives you SSH (login) access to your EC2 server. It does **not automatically grant** AWS CLI permission to interact with services like:

* EKS
* ECR
* S3
* EC2
* IAM

---

## ✅ Two Options to Give AWS CLI Permissions:

### 🔹 Option 1: Use `aws configure` (Best for Quick Setup)

Run on your EC2:

```bash
aws configure
```

Provide:

* Access Key ID
* Secret Access Key
* Region (e.g. `us-east-1`)
* Output format (e.g. `json`)

You get these keys from:

> AWS Console → IAM → Users → your-user → **Create access key**

> ⚠️ This method saves credentials in `~/.aws/credentials`

---

### 🔹 Option 2: Attach IAM Role to EC2 (Best Practice for Production)

1. Go to **EC2 Dashboard → Instances**
2. Select your EC2 → **Actions → Security → Modify IAM Role**
3. Attach a role with policies like:

   * `AmazonEKSFullAccess`
   * `AmazonEC2ContainerRegistryFullAccess`
   * `AmazonS3FullAccess` (optional)

✅ This way, AWS CLI will **auto-authenticate** without needing `aws configure`.

---

## 🔑 Summary

| You have `.pem` file | Need `aws configure`?                 | Notes                          |
| -------------------- | ------------------------------------- | ------------------------------ |
| ✅ Yes                | ✅ Yes (if using access keys manually) | For `aws eks`, `aws ecr`, etc. |
| ✅ Yes                | ❌ No (if EC2 has IAM role)            | Best practice for automation   |

## Full Policy
```bash
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "EC2Access",
			"Effect": "Allow",
			"Action": [
				"ec2:*",
				"ec2-instance-connect:*",
				"autoscaling:*",
				"imagebuilder:*",
				"elasticloadbalancing:*",
				"ec2:DescribeVolumes",
				"eks:*",
				"ecr:*"
				
			],
			"Resource": "*"
		}
	
	]
}
```

---


</details>
---
<details>
  <summary><strong>Create an ECR Repository</strong></summary>
	When you run the `aws ecr create-repository` command, **Amazon creates a new private container image repository** under your AWS account in the specified region.

---

## ✅ What Happens When You Create an ECR Repository?

### 🔧 Command Example:

```bash
aws ecr create-repository \
  --repository-name springboot-ecr \
  --region us-east-1
```

---

## 🔍 Results:

### 1. ✅ **A new private ECR repository is created**

* Repository URI:

  ```
  123456789012.dkr.ecr.us-east-1.amazonaws.com/springboot-ecr
  ```
* You can push/pull Docker images using this URI.

---

### 2. ✅ **You can now push Docker images to it**

After creating the repository, you can:

```bash
# Authenticate Docker to your ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Tag and push an image
docker tag my-app-image:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/springboot-ecr:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/springboot-ecr:latest
```

---

### 3. ✅ **It appears in the AWS Console**

You’ll see it in:

* **AWS Console → ECR → Repositories**
* You can view image tags, scan for vulnerabilities, etc.

---

## 📌 Nothing bad happens:

* You are **not charged** for creating the repo.
* You are only billed for:

  * **Storage** of images
  * **Data transfer** when pulling images

---

## 🛑 If the name already exists?

You get this error:

```bash
An error occurred (RepositoryAlreadyExistsException) ...
```

In that case, you can:

* Use the existing repo
* Or create a new one with a different name

---
Yes ✅ — `us-east-1` **is a predefined AWS region**.

---

## 🌍 AWS Regions Overview

`us-east-1` stands for:

> **US East (N. Virginia)**

It’s the **default and most popular region**, often used in tutorials and infrastructure setups.

---

### ✅ Common Predefined AWS Regions:

| Region Name              | Region Code      | Location            |
| ------------------------ | ---------------- | ------------------- |
| US East (N. Virginia)    | `us-east-1`      | 🇺🇸 North Virginia |
| US East (Ohio)           | `us-east-2`      | 🇺🇸 Ohio           |
| US West (N. California)  | `us-west-1`      | 🇺🇸 California     |
| US West (Oregon)         | `us-west-2`      | 🇺🇸 Oregon         |
| Asia Pacific (Mumbai)    | `ap-south-1`     | 🇮🇳 India          |
| Asia Pacific (Singapore) | `ap-southeast-1` | 🇸🇬 Singapore      |
| Asia Pacific (Tokyo)     | `ap-northeast-1` | 🇯🇵 Japan          |
| Europe (Frankfurt)       | `eu-central-1`   | 🇩🇪 Germany        |
| Europe (Ireland)         | `eu-west-1`      | 🇮🇪 Ireland        |

---

## 📌 How to Choose a Region?

✅ You can choose any region **when creating ECR, EKS, S3, etc.**, but:

* Pick a region **closest to your users or team**
* Stick to **one region** for ECR + EKS + EC2 (to avoid cross-region issues)
* `us-east-1` is perfectly fine and widely supported

---

## 🛠 Example (Creating ECR in `us-east-1`):

```bash
aws ecr create-repository \
  --repository-name springboot-ecr \
  --region us-east-1
```
## 🛠 OutPut (Creating ECR in `us-east-1`):

```bash
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-east-1:661062380194:repository/springboot-ecr",
        "registryId": "661062380194",
        "repositoryName": "springboot-ecr",
        "repositoryUri": "661062380194.dkr.ecr.us-east-1.amazonaws.com/springboot-ecr",
        "createdAt": "2025-06-20T16:05:30.326000+00:00",
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": false
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}
```

---



</details>
<details>
  <summary><strong>Install eksctl & kubectl</strong></summary>
	Here’s a clean, step-by-step guide to **install `eksctl` and `kubectl`** on your **Ubuntu EC2 instance** (like the one you're using):

---

## ✅ 1. Install `kubectl` (Kubernetes CLI)
It looks like the `curl -LO` command failed because the nested `$(curl -s ...)` didn't resolve properly — likely due to quoting issues or missing dependencies.

Let’s fix it by running **safe, step-by-step commands**:

---

## ✅ Correct Way to Install `kubectl` on Ubuntu

### 🔹 Step 1: Get the latest stable version into a variable

```bash
KUBECTL_VERSION=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
```

### 🔹 Step 2: Download the binary

```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
```

### 🔹 Step 3: Make it executable and move it to PATH

```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin
```

### 🔹 Step 4: Test it

```bash
kubectl version --client
```


---

## ✅ 2. Install `eksctl` (EKS Management CLI)

```bash
curl --silent --location "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin
```

### 🔍 Verify:

```bash
eksctl version
```

---

## ✅ What Next?

Once installed, you can:

* 🚀 Create a cluster:

  ```bash
  eksctl create cluster --name springboot-cluster --region us-east-1 --nodes 2
  ```

* 🔗 Connect to it:

  ```bash
  aws eks update-kubeconfig --region us-east-1 --name springboot-cluster
  kubectl get nodes
  ```

---

Great question!

### ✅ "ctl" stands for **"control"**.

In tools like `kubectl` or `eksctl`, it’s a short form used in the command-line tool name to indicate it’s used for **controlling** or **managing** something.

---

## 🔍 Examples:

| Tool      | Full Meaning           | Purpose                                                 |
| --------- | ---------------------- | ------------------------------------------------------- |
| `kubectl` | **Kubernetes control** | Controls Kubernetes clusters (`kubectl get pods`, etc.) |
| `eksctl`  | **EKS control**        | Creates and manages AWS EKS clusters                    |
| `helm`    | (Not using ctl)        | Controls Helm charts for Kubernetes                     |

---

So:

* `kubectl` = tool to **control Kubernetes**
* `eksctl` = tool to **control AWS EKS**

---


</details>
<details>
  <summary><strong>Create an EKS Cluster</strong></summary>
	Great to hear everything is working now! Here's a **step-by-step documented guide** to create an Amazon EKS cluster using `eksctl`, with detailed **troubleshooting** instructions and **CloudFormation console link** usage:

---

## ✅ Step-by-Step: Create Amazon EKS Cluster with `eksctl`

### 📌 Prerequisites

1. **IAM permissions required**:
   Your IAM user or role must have the following permissions:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "FullAccessForEKSSetup",
         "Effect": "Allow",
         "Action": [
           "ec2:*",
           "ec2-instance-connect:*",
           "autoscaling:*",
           "imagebuilder:*",
           "elasticloadbalancing:*",
           "eks:*",
           "ecr:*",
           "cloudformation:*",
           "iam:*"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

2. **Install Tools**:

   * `kubectl`
   * `eksctl`
   * `aws` CLI

---

## 🚀 Create Cluster Command

```bash
eksctl create cluster \
  --name springboot-cluster \
  --version 1.29 \
  --region us-east-1 \
  --nodegroup-name linux-nodes \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

---

## 🔍 If an Error Happens (e.g. Access Denied, Stack Creation Fails)

1. Visit **CloudFormation Console** for the specified region:
   👉 [https://console.aws.amazon.com/cloudformation/home?region=us-east-1](https://console.aws.amazon.com/cloudformation/home?region=us-east-1)

2. In the CloudFormation console:

   * Look for a stack named:
     `eksctl-springboot-cluster-cluster`
   * If it's in `ROLLBACK_IN_PROGRESS` or `ROLLBACK_FAILED` state:

     * Click on it.
     * Go to **Events** tab.
     * Look for `CREATE_FAILED` lines for specific reasons (usually permission-related).

3. Common Errors and Fixes:

| ❌ Error                                          | ✅ Solution                                                                                                                            |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `AccessDenied`                                   | Your IAM role/user lacks permission. Add missing IAM actions (e.g., `iam:CreateRole`, `iam:PassRole`, `iam:CreateServiceLinkedRole`). |
| `AlreadyExistsException`                         | The stack or cluster already exists. Use a different name or delete the existing one via the console.                                 |
| `Resource creation cancelled`                    | Usually caused by earlier failures in the stack. Check the first `CREATE_FAILED` reason.                                              |
| `eksctl delete cluster` says "cluster not found" | Manually delete the CloudFormation stacks via the [console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1).     |

---

## ✅ Clean Up (If Needed)

If your stack is broken or half-created:

```bash
eksctl delete cluster --name springboot-cluster --region us-east-1
```

If that fails, go to CloudFormation console → Delete the stack manually:
👉 [https://console.aws.amazon.com/cloudformation/home?region=us-east-1](https://console.aws.amazon.com/cloudformation/home?region=us-east-1)

---

## 🟢 After Success

After a successful cluster creation:

```bash
kubectl get nodes
```

This should show your EKS nodes.

---


</details>
<details>
  <summary><strong>Configure kubectl to talk to your EKS cluster</strong></summary>
	Perfect! Let’s get you connected and deploy your Spring Boot app step-by-step.

### 1. **Configure kubectl to talk to your EKS cluster**

Run this command to update your kubeconfig file with your new cluster info:

```bash
aws eks --region us-east-1 update-kubeconfig --name springboot-cluster-v2
```

To verify, run:

```bash
kubectl get nodes
```

You should see the nodes in your cluster listed.

---

</details>
<details>
  <summary><strong>Policy</strong></summary>
</details>
<details>
  <summary><strong>Policy</strong></summary>
</details>
<details>
  <summary><strong>Policy</strong></summary>
</details>
</details>



### 🔁 How They Work Together

Example:

* You create a **Group** called `Admins`, attach an **AdminAccess Policy** to it.
* Add **Users** `Alice` and `Bob` to the `Admins` group.
* You create a **Role** named `S3BackupRole` which allows full access to an S3 bucket.
* EC2 service assumes this **Role** to upload backups.

---

### Summary Table

| Component   | Description                                            | Use Case                                 |
| ----------- | ------------------------------------------------------ | ---------------------------------------- |
| **Policy**  | Rules that define what actions are allowed or denied   | Allow EC2 to read from S3                |
| **Service** | AWS feature (e.g., EC2, S3)                            | Used with IAM roles or users             |
| **Role**    | Temporary permission set, assumed by services or users | Lambda function needs access to DynamoDB |
| **User**    | Individual identity (person/app) with credentials      | Developer logging into AWS Console       |
| **Group**   | Collection of users sharing permissions                | All admins get `AdministratorAccess`     |

---


