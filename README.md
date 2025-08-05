# 🔐 AWS Project: Encrypting and Decrypting Data using AWS KMS 

This project demonstrates how to use **AWS Key Management Service (KMS)** to securely encrypt, decrypt, and re-encrypt sensitive data. AWS KMS is a fully managed service that simplifies key creation and control, essential for protecting data at rest in modern cloud environments. 

---

## 🌐 Overview

This project helped me understand how encryption workflows operate within AWS using customer-managed keys. I created and configured IAM groups and users for key usage and administration, launched a Linux-based EC2 instance, and used the AWS CLI to encrypt, decrypt, and re-encrypt data files with a KMS key. These techniques are vital for real-world use cases such as secure data storage, file-level access control, and compliance with industry standards like SOC and PCI-DSS.

##  Key Features of AWS KMS

- **Hardware-based key protection** using FIPS 140-2 validated HSMs.
- **Seamless integration** with other AWS services (EC2, S3, RDS, etc.).
- **Granular access control** via IAM and key policies.
- **Audit trails** with AWS CloudTrail for compliance and traceability.
- **Certifications** including SOC 1/2/3, PCI DSS Level 1, and FedRAMP.

---
## 🗺️ Architecture Diagram

<img width="3769" height="2100" alt="image" src="https://github.com/user-attachments/assets/aca484c2-145e-4d9e-a993-30a547db88bd" />

---
## 🧱 Key AWS Services Used

- **AWS KMS** – Manages encryption keys to protect data.
- **Amazon S3** – Stores objects with encryption and supports cross-region replication.
- **Amazon EBS** – Provides encrypted block storage for EC2 instances.
- **Amazon AMI** – Enables creation of encrypted machine images for secure deployments.
- **IAM** – Controls access to encryption keys and resources.
- **CloudWatch Logs** – (Optional) Monitors and logs encryption-related activity.

---
## 🛠️ Deployment Steps

### ✅ Step 1 –Create a Users and User Group

In IAM i create a group named `KMSGroup` and attache to it th following policy that i gave it the name KMS_Policy

````
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "kms:*",
            "Resource": "*"
        }
    ]
}
````

Then, i created two users: **KeyManager** and  **KeyEncryption**. I provided them with **AWS Management Console** before adding them to `KMSGroup`

 For KeyEncryption user  i created an  **Access key** for CLI use and downloaded the `.csv` file the need in later configuration
 
<img width="1626" height="120" alt="37" src="https://github.com/user-attachments/assets/762e99f7-2fd7-429e-8e6e-e0834b35ed04" />

---

### ✅ Step 2 – Create a KMS Key

In AWS KMS, I created a key where:
- **Key type**: Symmetric  
- **Key usage**: Encrypt and decrypt  
- **Alias**: `Admin`  
- **Key administrative permissions**: `KeyManager`  
- **Key usage permissions**:`KeyEncryption`  

<img width="1919" height="429" alt="39" src="https://github.com/user-attachments/assets/764b9549-59ea-43f2-9bb7-65a0c1f0b908" />


Note: I copied the **Key ID** for later use

---

### ✅ Step 3 –  Launch an EC2 Instance nd SSH into the EC2 Instance

I launched a t2.micro instance with the name `MyEC2Server` and Amazon Linux as an OS. For the key pair, I Created a one named `MyKey` (type: RSA, format: `.pem`).

After launching, i connect into the ec2 instance via SSH

```bash
ssh -i MyKey.pem ec2-user@<EC2-PUBLIC-IP>

<img width="1535" height="371" alt="image" src="https://github.com/user-attachments/assets/1286d426-55ef-4698-a75e-a8f2ef079a05" />


### ✅ Step 4 – Perform KMS Encryption and Decryption

Once connected to the instance, perform the following steps:

**Configure AWS CLI with KeyEncryption credentials:**

```bash
aws configure
```

Then Provide the following: Access Key ID  | Secret Access Key | Region
  
<img width="1915" height="141" alt="image" src="https://github.com/user-attachments/assets/80965020-2de3-4577-9e7d-2d1f1c11006b" />

**Create a plaintext file:**

```bash
echo "Welcome to Nidhal's Lab" > secret.txt
```

**Encrypt the file and View encrypted content:**

```bash
aws kms encrypt \
  --key-id <replace-key-id> \
  --plaintext fileb://secret.txt \
  --output text \
  --query CiphertextBlob > encryptedsecret.txt
```
```bash
cat encryptedsecret.txt
```
<img width="1898" height="322" alt="42" src="https://github.com/user-attachments/assets/87060ede-16ea-40d4-b164-62f6357ea40d" />


**Decrypt the file and View decrypted content:**

```bash
aws kms decrypt \
  --ciphertext-blob fileb://encryptedsecret.txt \
  --output text \
  --query Plaintext > decryptedsecret.txt
```
<img width="1280" height="164" alt="image" src="https://github.com/user-attachments/assets/d54d2d5a-4c9a-4c6b-9a76-6ea94c15fc5d" />


**Re-encrypt the file:**

```bash
aws kms encrypt \
  --key-id <replace-key-id> \
  --plaintext fileb://decryptedsecret.txt \
  --output text \
  --query CiphertextBlob > newencryptedsecret.txt
```
<img width="1885" height="281" alt="image_2025-08-04_15-41-41" src="https://github.com/user-attachments/assets/f9303f1a-c945-4054-b67a-2d3f48b97ce3" />

**List all generated files:**

```bash
ls -lrt
```
<img width="1682" height="170" alt="44" src="https://github.com/user-attachments/assets/da92b54e-04c8-4925-867e-a7758c6db005" />


✅ I have now successfully encrypted, decrypted, and re-encrypted data on an EC2 instance using AWS KMS via the AWS CLI. 🎉 This workflow is foundational for real-world use cases like secure file exchange, protected backups, and regulated data processing pipelines.

---

## ✍️ Author

Made with 💻 by **Nidhal Labri**  
🔗 [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)

---

> _Secure your cloud. Secure your future._ ☁️🔒
