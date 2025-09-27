      Created by : Eskander Boukhari
# Scalable Web Application with ALB and Auto Scaling

A step-by-step implementation of a highly available web application with VPC, NAT Gateways, ALB, and Auto Scaling.

![Animation](images/Animation.gif)

---

## Lab 1: Creating the VPC and Subnets 🏗️

### **Create a Custom VPC**  
   - Name tag: "WebApp_VPC"
   - CIDR Block: `192.168.0.0/16`  

![Lab1-Img1](images/Lab1-Img1.png)

   - Enable **DNS hostnames** (VPC Settings > Actions).

![Lab1-Img2](images/Lab1-Img2.png)

![Lab1-Img3](images/Lab1-Img3.png)

### **Internet Gateway**
- Create and attach to the VPC.
- Name tag: "WebApp_VPC_IG"

![Lab1-Img4](images/Lab1-Img4.png)

![Lab1-Img5](images/Lab1-Img5.png)

![Lab1-Img6](images/Lab1-Img6.png)

### **Public Subnets**
| Subnet Name             |   AZ        | CIDR Block       |
|-------------------------|-------------|------------------|
| `Public_Subnet1_WebApp` | `us-east-1a`| `192.168.10.0/24`|
| `Public_Subnet2_WebApp` | `us-east-1b`| `192.168.20.0/24`|

![Lab1-Img7](images/Lab1-Img7.png)

![Lab1-Img8](images/Lab1-Img8.png)

![Lab1-Img9](images/Lab1-Img9.png)

### **Route Table**: 
- Name: `WebApp_Public_RT` with route `0.0.0.0/0` → Internet Gateway "WebApp_VPC_IG".

![Lab1-Img10](images/Lab1-Img10.png)

![Lab1-Img11](images/Lab1-Img11.png)

![Lab1-Img12](images/Lab1-Img12.png)

- Enable **Auto-assign public IPv4** for both subnets.

![Lab1-Img13](images/Lab1-Img13.png)

![Lab1-Img14](images/Lab1-Img14.png)

![Lab1-Img15](images/Lab1-Img15.png)

### **Private Subnets**
| Subnet Name               |   AZ        | CIDR Block        |
|---------------------------|-------------|-------------------|
| `Private_Subnet1_WebApp`  | `us-east-1a`| `192.168.100.0/24`|
| `Private_Subnet2_WebApp`  | `us-east-1b`| `192.168.200.0/24`|

![Lab1-Img16](images/Lab1-Img16.png)

![Lab1-Img17](images/Lab1-Img17.png)

![Lab1-Img18](images/Lab1-Img18.png)


## Lab 2: Creating Two NAT Gateways 🌐

### **NAT Gateways**
- **NAT Gateway A**: Deploy in `Public_Subnet1_WebApp` (us-east-1a) with Elastic IP.

![Lab2-Img1](images/Lab2-Img1.png)


- **NAT Gateway B**: Deploy in `Public_Subnet2_WebApp` (us-east-1b) with Elastic IP.

![Lab2-Img2](images/Lab2-Img2.png)

![Lab2-Img3](images/Lab2-Img3.png)

### **Private Route Tables**
| Route Table      | Target           |
|------------------|------------------|
| `WebApp_Private_RT1`   | NAT Gateway A    |
| `WebApp_Private_RT2`   | NAT Gateway B    |

![Lab2-Img4](images/Lab2-Img4.png)

![Lab2-Img5](images/Lab2-Img5.png)

![Lab2-Img6](images/Lab2-Img6.png)

![Lab2-Img7](images/Lab2-Img7.png)

![Lab2-Img8](images/Lab2-Img8.png)

![Lab2-Img9](images/Lab2-Img9.png)

![Lab2-Img10](images/Lab2-Img10.png)

![Lab2-Img11](images/Lab2-Img11.png)

![Lab2-Img12](images/Lab2-Img12.png)

![Lab2-Img13](images/Lab2-Img13.png)

![Lab2-Img14](images/Lab2-Img14.png)
---

## Lab 3: IAM Role and EC2 Instances 🔑

### **IAM Role for SSM**
- **Role Name**: `WebApp_TO_SSM`
- Attach **AmazonSSMManagedInstanceCore** policy.

![Lab3-Img1](images/Lab3-Img1.png)

![Lab3-Img2](images/Lab3-Img2.png)

![Lab3-Img3](images/Lab3-Img3.png)

![Lab3-Img4](images/Lab3-Img4.png)

### **EC2 Deployment**

- **Security Group**: `WebApp_SG` (Inbound: HTTP/80 | Outbound: All).
![Lab3-Img5](images/Lab3-Img5.png)

![Lab3-Img6](images/Lab3-Img6.png)

![Lab3-Img7](images/Lab3-Img7.png)

![Lab3-Img8](images/Lab3-Img8.png)

- **Instances**:
  - `WebApp_Server1` in `Private_Subnet1`
  - `WebApp_Server2` in `Private_Subnet2`
- **Specs**: `t2.micro`, Amazon Linux 2023 AMI.

![Lab3-Img9](images/Lab3-Img9.png)

![Lab3-Img10](images/Lab3-Img10.png)

- **IAM Role**: Attach `WebApp_TO_SSM`.


![Lab3-Img11](images/Lab3-Img11.png)

- **User Data Script**

  - [Script-WebApp_Server1](files/Script-WebApp_Server1.sh)

  - [Script-WebApp_Server2](files/Script-WebApp_Server2.sh)

![Lab3-Img12](images/Lab3-Img12.png)

### **Validation**  
   - Connect via **SSM Session Manager**:  
 
 ![Lab3-Img13](images/Lab3-Img13.png)   
 
    sudo systemctl status httpd

![Lab3-Img14](images/Lab3-Img14.png)

curl http://"instanceprivateip"

![Lab3-Img15](images/Lab3-Img15.png)

![Lab3-Img16](images/Lab3-Img16.png)


## Lab 4: Application Load Balancer (ALB) ⚖️

### **Target Group**  
   - Name: **WebApp-TG** (Port 80, HTTP)  
   - Register both EC2 instances.  

![Lab4-Img1](images/Lab4-Img1.png)

![Lab4-Img2](images/Lab4-Img2.png)

![Lab4-Img3](images/Lab4-Img3.png)

![Lab4-Img4](images/Lab4-Img4.png)

![Lab4-Img5](images/Lab4-Img5.png)

![Lab4-Img6](images/Lab4-Img6.png)

### **Security Group**  
   - **ALBSG**: Allow inbound HTTP/80 from anywhere.  

![Lab4-Img7](images/Lab4-Img7.png)

### **Create ALB**  
   - Name: **WebALB** (Internet-facing)  
   - Attach to **Public_Subnet1** and **Public_Subnet2**.  
   - Test ALB DNS name in a browser after instances are healthy.  

![Lab4-Img8](images/Lab4-Img8.png)

![Lab4-Img9](images/Lab4-Img9.png)

![Lab4-Img10](images/Lab4-Img10.png)

![Lab4-Img11](images/Lab4-Img11.png)

![Lab4-Img12](images/Lab4-Img12.png)

![Lab4-Img13](images/Lab4-Img13.png)

![Lab4-Img14](images/Lab4-Img14.png)

## Lab 5: Auto Scaling and Security Hardening 🔒

### **Auto Scaling Group**

#### **Launch Template**  
- Name `WebApp_Template`
- Use `t2.micro`, AMI: **Amazon Linux 2023**, Security Group: **WebApp_SG**.  

![Lab5-Img1](images/Lab5-Img1.png)

![Lab5-Img2](images/Lab5-Img2.png)

![Lab5-Img3](images/Lab5-Img3.png)

![Lab5-Img4](images/Lab5-Img4.png)

- user data [Script-LaunchTemplate](files/Script-LaunchTemplate.sh)   

![Lab5-Img5](images/Lab5-Img5.png)

#### **Auto Scaling Group**  
- Name: **WebApp_ASG**  

![Lab5-Img6](images/Lab5-Img6.png)

- Subnets: `Private_Subnet1` and `Private_Subnet2`  

![Lab5-Img7](images/Lab5-Img7.png)

   - Desired: 4 | Min: 2 | Max: 6  
   - Attach to **WebApp-TG** and enable ALB health checks.  

![Lab5-Img8](images/Lab5-Img8.png)

![Lab5-Img9](images/Lab5-Img9.png)

![Lab5-Img10](images/Lab5-Img10.png)

![Lab5-Img11](images/Lab5-Img11.png)

![Lab5-Img12](images/Lab5-Img12.png)

#### **Validation**  
- Terminate original instances; confirm ASG replaces them.  

![Lab5-Img13](images/Lab5-Img13.png)

- Test ALB DNS again.  

![Lab5-Img14](images/Lab5-Img14.png)

### **Security Group Lockdown** 

#### **Update WebApp_SG**  
   - Allow inbound HTTP/80 **only from ALBSG**.  

#### **Update ALBSG**  
   - Allow outbound HTTP/80 to **WebApp_SG**.  

![Lab5-Img15](images/Lab5-Img15.png)


## Lab 6: Cleanup 🧹

**Delete resources in this order:**  
1. Terminate EC2 instances.  

![Lab6-Img8](images/Lab6-Img8.png)

2. Delete Auto Scaling Group and Launch Template.  

![Lab6-Img1](images/Lab6-Img1.png)

![Lab6-Img6](images/Lab6-Img6.png)

3. Delete ALB, Target Group (**WebTG**), and **ALBSG**. 

![Lab6-Img2](images/Lab6-Img2.png)

![Lab6-Img3](images/Lab6-Img3.png)

4. Delete NAT Gateways and release Elastic IPs.  

![Lab6-Img4](images/Lab6-Img4.png)

![Lab6-Img5](images/Lab6-Img5.png)

5. Delete subnets, route tables, Internet Gateway, and VPC.  

![Lab6-Img7](images/Lab6-Img7.png)


⚠️ **Important**: Clean up all resources to avoid unexpected charges. 

