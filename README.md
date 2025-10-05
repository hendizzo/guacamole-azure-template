# Apache Guacamole on Azure - One-Click Deployment

Deploy Apache Guacamole with high availability on Microsoft Azure using a single click. This template provides a production-ready deployment with load balancing, SSL/TLS support, and automated setup.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhendizzo%2Fguacamole-azure-template%2Fmain%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fhendizzo%2Fguacamole-azure-template%2Fmain%2FcreateUiDefinition.json)

## 🚀 What is Apache Guacamole?

Apache Guacamole is a **clientless remote desktop gateway** that supports standard protocols like VNC, RDP, and SSH. Access your remote desktops through any modern web browser without installing client software.

**Key Benefits:**
- 🌐 **Clientless Access** - No software installation required
- 🔒 **Secure** - HTTPS encryption and centralized authentication
- 📱 **Cross-Platform** - Works on any device with a web browser
- ⚡ **High Performance** - Optimized for low-latency connections

## 🏗️ Architecture

This deployment creates a **high-availability** setup with:

```
Internet → Load Balancer → [ VM1 | VM2 ] → Guacamole + PostgreSQL
```

### Components:
- **🔄 Azure Load Balancer** - Traffic distribution and health monitoring
- **🖥️ Virtual Machines** - Ubuntu 22.04 LTS with Docker
- **🐳 Docker Containers** - Guacamole, PostgreSQL, and Nginx
- **🔒 SSL/TLS** - HTTPS encryption with Let's Encrypt support
- **💾 PostgreSQL Database** - Configuration and connection storage
- **🛡️ Network Security** - Configured security groups and firewall rules

## ✨ Features

### ✅ **Production Ready**
- High availability with multiple VMs
- Load balancer with health probes
- Automated SSL certificate management
- Database persistence and backups

### ✅ **Easy Deployment**
- One-click Azure Portal deployment
- Interactive parameter configuration
- Automated setup via cloud-init
- No manual configuration required

### ✅ **Secure by Default**
- HTTPS-only access
- Network security groups
- Configurable IP restrictions
- SSH key or password authentication

### ✅ **Cost Optimized**
- Optional auto-shutdown schedules
- Configurable VM sizes
- Efficient resource utilization
- Pay-as-you-use model

## 🎯 Quick Start

### 1. **Deploy to Azure**
Click the **Deploy to Azure** button above and fill in the parameters:

#### Basic Configuration:
- **Project Name** - Unique identifier (3-11 characters)
- **Admin Username** - VM administrator account
- **Admin Password** - Secure password (12+ characters)

#### Network Settings:
- **Virtual Network** - Network configuration
- **Allowed Source IPs** - IP restrictions for security
- **SSH Access** - Enable for management (optional)

#### Guacamole Settings:
- **Custom Domain** - Your domain name (optional)
- **Email Address** - For SSL certificates
- **Database Password** - PostgreSQL password (auto-generated if empty)

#### Advanced Options:
- **VM Size** - Choose based on your workload
- **VM Count** - Number of instances (2 recommended)
- **Auto-Shutdown** - Daily shutdown for cost savings

### 2. **Access Guacamole**
After deployment (5-10 minutes):
1. Navigate to the provided URL
2. Accept the temporary SSL certificate
3. Login with default credentials:
   - **Username:** `guacadmin`
   - **Password:** `guacadmin`
4. **⚠️ Change the password immediately!**

### 3. **Configure SSL (Optional)**
For custom domains with Let's Encrypt:
```bash
ssh admin@your-vm-ip
sudo /opt/letsencrypt-setup.sh your-domain.com
```

## 📋 Deployment Parameters

| Parameter | Description | Default | Required |
|-----------|-------------|---------|----------|
| **Project Name** | Resource prefix (3-11 chars) | `guacamole` | ✅ |
| **Admin Username** | VM administrator username | `azureuser` | ✅ |
| **Admin Password** | VM administrator password | - | ✅ |
| **VM Size** | Azure VM size | `Standard_D2s_v3` | ✅ |
| **VM Count** | Number of VMs (1-5) | `2` | ✅ |
| **Domain Name** | Custom domain (optional) | - | ❌ |
| **Email Address** | For SSL certificates | - | ❌ |
| **Database Password** | PostgreSQL password | Auto-generated | ❌ |
| **Allowed Source IPs** | IP access restrictions | `*` (any) | ✅ |
| **Enable SSH** | SSH access to VMs | `true` | ✅ |
| **Auto-Shutdown** | Daily shutdown schedule | `false` | ❌ |

## 🔧 Post-Deployment

### **Initial Setup**
1. **Change Default Password**: Login and update admin credentials
2. **Configure Connections**: Add your remote desktop/server connections
3. **User Management**: Create additional user accounts
4. **SSL Setup**: Configure custom domain and certificates

### **Management Commands**
```bash
# SSH into VM
ssh admin@your-vm-ip

# Check container status
sudo docker ps

# View logs
sudo docker logs guacamole_guacamole
sudo docker logs guacamole_nginx

# Restart services
cd /opt/guacamole
sudo docker-compose restart

# Setup SSL certificate
sudo /opt/letsencrypt-setup.sh your-domain.com
```

### **Monitoring**
- **Health Endpoint**: `https://your-domain/health`
- **Load Balancer**: Azure Portal → Load Balancer metrics
- **VM Metrics**: Azure Monitor integration
- **Container Logs**: Docker logs via SSH

## 🛠️ Customization

### **VM Sizing Guidelines**
| Workload | VM Size | vCPUs | RAM | Concurrent Users |
|----------|---------|--------|-----|------------------|
| **Small** | Standard_B2s | 2 | 4GB | 5-10 |
| **Medium** | Standard_D2s_v3 | 2 | 8GB | 10-20 |
| **Large** | Standard_D4s_v3 | 4 | 16GB | 20-40 |
| **XLarge** | Standard_D8s_v3 | 8 | 32GB | 40-80 |

### **Network Security**
- **Restrict IP Access**: Replace `*` with your IP range (e.g., `203.0.113.0/24`)
- **SSH Security**: Limit SSH access to management IPs only
- **Firewall Rules**: Additional NSG rules via Azure Portal

### **SSL Certificates**
- **Let's Encrypt**: Automated with domain verification
- **Custom Certificates**: Upload via SSH and restart containers
- **Wildcard Certificates**: Supported for multiple subdomains

## 🚨 Troubleshooting

### **Common Issues**

#### **502 Bad Gateway**
```bash
# Check container status
sudo docker ps -a

# Restart containers
cd /opt/guacamole
sudo docker-compose restart
```

#### **Health Probe Failures**
```bash
# Test health endpoint
curl -k https://localhost:8443/health

# Check nginx logs
sudo docker logs guacamole_nginx
```

#### **Database Connection Issues**
```bash
# Test database connection
sudo docker exec -it guacamole_postgres psql -U guacamole -d guacamole_db

# Reset database (if needed)
cd /opt/guacamole
sudo docker-compose down
sudo docker volume rm guacamole_postgres_data
sudo docker-compose up -d
```

#### **Domain/DNS Issues**
```bash
# Verify DNS resolution
nslookup your-domain.com

# Check certificate
openssl s_client -connect your-domain.com:443
```

### **Support Resources**
- 📖 **Apache Guacamole Documentation**: [guacamole.apache.org](https://guacamole.apache.org/doc/)
- 🐳 **Docker Hub Images**: [hub.docker.com/u/guacamole](https://hub.docker.com/u/guacamole)
- 💬 **Community Support**: [Apache Guacamole Users Mailing List](https://guacamole.apache.org/support/)

## 💰 Cost Optimization

### **Estimated Monthly Costs** (UK West region)
| Configuration | VM Size | Storage | Load Balancer | **Total/Month** |
|---------------|---------|---------|---------------|------------------|
| **Dev/Test** | 1x B2s | Standard | Standard | **~$50-70** |
| **Production** | 2x D2s_v3 | Premium | Standard | **~$150-200** |
| **High Load** | 2x D4s_v3 | Premium | Standard | **~$300-400** |

### **Cost Saving Tips**
- ✅ **Auto-Shutdown**: Enable daily shutdown for dev environments
- ✅ **Reserved Instances**: 1-3 year commitments for production
- ✅ **Spot Instances**: For non-critical workloads (manual setup)
- ✅ **Right-Sizing**: Start small and scale up based on usage

## 🔐 Security Best Practices

### **Network Security**
- 🛡️ **IP Restrictions**: Limit access to known IP ranges
- 🔒 **SSH Keys**: Use SSH keys instead of passwords
- 🚫 **Disable SSH**: Turn off SSH access after setup (if not needed)
- 🔍 **Network Monitoring**: Enable Azure Network Watcher

### **Application Security**
- 🔑 **Strong Passwords**: Enforce complex password policies
- 👥 **User Management**: Regular user account audits
- 📝 **Session Logging**: Enable connection logging and monitoring
- 🔄 **Regular Updates**: Keep containers and OS updated

### **SSL/TLS Security**
- 📜 **Valid Certificates**: Use Let's Encrypt or commercial certificates
- 🔒 **TLS 1.2+**: Disable older SSL/TLS versions
- 🚫 **HTTP Redirect**: Ensure all traffic uses HTTPS
- 🔍 **Certificate Monitoring**: Monitor certificate expiration

## 📊 Monitoring & Alerting

### **Azure Monitor Integration**
- 📈 **VM Metrics**: CPU, memory, disk, network utilization
- 🔄 **Load Balancer**: Health probe status and traffic distribution
- 📊 **Application Insights**: Custom metrics and logs (optional)
- 🚨 **Alerts**: Automated notifications for issues

### **Health Checks**
- ⚡ **Application Health**: `/health` endpoint monitoring
- 🔍 **Container Status**: Docker container health monitoring
- 💾 **Database Health**: PostgreSQL connection and performance
- 🌐 **Network Connectivity**: End-to-end connection testing

## 🤝 Contributing

We welcome contributions to improve this deployment template:

1. **Fork the Repository**
2. **Create a Feature Branch**
3. **Make Your Changes**
4. **Test the Deployment**
5. **Submit a Pull Request**

### **Areas for Contribution**
- 🔧 Additional ARM template features
- 📖 Documentation improvements
- 🐛 Bug fixes and optimizations
- 🎨 UI definition enhancements

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Apache Software Foundation** - For Apache Guacamole
- **Microsoft Azure** - For cloud platform and ARM templates
- **Docker Community** - For containerization support
- **Let's Encrypt** - For free SSL certificates

---

**⭐ Star this repository if it helped you deploy Guacamole on Azure!**

> **Need Help?** Create an issue in this repository or check the troubleshooting section above.