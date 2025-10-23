# a428-cicd-labs
# CI/CD Pipeline with Jenkins - Final Project

## 📝 Project Overview
Complete CI/CD pipeline implementation using Jenkins, Docker, Prometheus, and Grafana for automated build, test, and deployment of a React application.

## 🏗️ Architecture Components

- **Jenkins** (Port 8080): CI/CD orchestration server
- **React Application** (Port 3000): Web application
- **Prometheus** (Port 9091): Metrics collection and monitoring
- **Grafana** (Port 3031): Visualization dashboard
- **cAdvisor** (Port 8081): Container metrics exporter

## 🔄 Pipeline Stages

1. **Build** - Install dependencies using npm
2. **Test** - Run automated test suite
3. **Manual Approval** - Human approval gate before deployment
4. **Deploy** - Deploy application and run for 1 minute

## 🚀 Quick Start

### Prerequisites
- Docker and Docker Compose installed
- Git installed
- Minimum 8GB RAM
- Ports available: 8080, 9091, 3031, 8081, 3000

### Clone Repository
```bash
git clone https://github.com/Budiman002/a428-cicd-labs.git
cd a428-cicd-labs
```

### Start All Services
```bash
# Create Docker network
docker network create jenkins

# Start Jenkins
docker run -d --name jenkins --network jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts

# Fix Docker socket permission
docker exec -u root jenkins chmod 666 /var/run/docker.sock

# Start Prometheus
docker run -d --name prometheus --network jenkins \
  -p 9091:9090 \
  -v ~/prometheus-config:/etc/prometheus \
  prom/prometheus

# Start Grafana
docker run -d --name grafana --network jenkins \
  -p 3031:3000 \
  grafana/grafana

# Start cAdvisor
docker run -d --name cadvisor --network jenkins \
  -p 8081:8080 \
  -v /:/rootfs:ro \
  -v /var/run:/var/run:ro \
  -v /sys:/sys:ro \
  -v /var/lib/docker/:/var/lib/docker:ro \
  gcr.io/cadvisor/cadvisor:latest
```

### Access Services
- Jenkins: http://localhost:8080
- Prometheus: http://localhost:9091
- Grafana: http://localhost:3031 (default: admin/admin)
- cAdvisor: http://localhost:8081

## 📊 Monitoring Configuration

### Prometheus Setup
Configuration file: `~/prometheus-config/prometheus.yml`
```yaml
global:
  scrape_interval: 10s
  evaluation_interval: 10s

scrape_configs:
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

### Grafana Dashboard
Dashboard includes 3 monitoring panels:
1. **Container Memory Usage** - Time series visualization
2. **Container CPU Usage** - Time series visualization
3. **All Containers Memory** - Stat panel with current values

## 🔄 Running the Pipeline

1. Open Jenkins: http://localhost:8080
2. Navigate to pipeline: "submission-cicd-pipeline-Budiman002"
3. Click **"Build Now"**
4. Wait for **Manual Approval** stage
5. Click **"Proceed"** to continue deployment
6. Application will run for 1 minute, then automatically stop

## 📁 Project Structure
```
a428-cicd-labs/
├── jenkins/
│   ├── Jenkinsfile           # Pipeline configuration
│   └── scripts/
│       ├── test.sh           # Test execution script
│       ├── deliver.sh        # Deployment script
│       └── kill.sh           # Cleanup script
├── public/                   # React public assets
├── src/                      # React source code
├── package.json              # Node.js dependencies
└── README.md                 # This file
```

## 🐛 Troubleshooting

### Docker Permission Denied
If pipeline fails with "permission denied" error:
```bash
docker exec -u root jenkins chmod 666 /var/run/docker.sock
```

### Jenkins Not Starting
Check logs and restart:
```bash
docker logs jenkins --tail 50
docker restart jenkins
```

### Prometheus Not Scraping
Verify configuration and targets:
```bash
# Check config file
cat ~/prometheus-config/prometheus.yml

# Check targets status
http://localhost:9091/targets
```

## 🎓 Implementation Details

### Kriteria 1: Repository Setup ✅
- Repository forked from: dicodingacademy/a428-cicd-labs
- Jenkinsfile configured with all required stages
- Git repository properly initialized

### Kriteria 2: Deploy Stage ✅
- Deploy stage implemented in Jenkinsfile
- Uses deliver.sh script for deployment
- Application successfully deployed via Docker

### Kriteria 3: Sleep Implementation ✅
- Sleep 60 seconds (1 minute) after deployment
- Application runs during sleep period
- Automatic cleanup after 1 minute using kill.sh

### Kriteria 4: Manual Approval ✅
- Manual Approval stage before Deploy
- Input message: "Lanjutkan ke tahap Deploy?"
- Buttons: Proceed / Abort

### Kriteria 5: Monitoring ✅
- Prometheus collecting metrics every 10 seconds
- Grafana dashboard visualizing container metrics
- cAdvisor exporting container statistics
- Custom ports implemented (Grafana: 3031, Prometheus: 9091)

## 📚 Tech Stack

- **Jenkins** 2.528.1 - CI/CD automation
- **Docker** 24.x - Containerization
- **Prometheus** latest - Metrics collection
- **Grafana** latest - Visualization
- **cAdvisor** latest - Container monitoring
- **Node.js** LTS Buster Slim - Runtime
- **React** 18.x - Frontend framework
---

**Note:** This pipeline demonstrates core CI/CD concepts including automated testing, manual approval gates, and container monitoring. All components run in Docker containers for easy deployment and scalability.
```

---

### **Step 5: Preview Changes (Optional)**

1. Click tab **"Preview"** di atas editor
2. Review perubahan untuk memastikan formatting correct
3. Klik tab **"Edit"** untuk kembali ke mode edit jika perlu koreksi

---

### **Step 6: Commit Changes**

1. Scroll ke bawah sampai section **"Commit changes"**
2. Di field **"Commit message"**, ketik:
```
   Add complete documentation for final submission
```
3. Di field description (optional), bisa tambahkan:
```
   - Added architecture overview
   - Added setup instructions
   - Added troubleshooting guide
   - Added implementation details for all 5 kriteria
```
4. Pilih **"Commit directly to the `main` branch"** (default)
5. Click tombol hijau **"Commit changes"**

---

### **Step 7: Verify Changes**

1. Akan kembali ke halaman repository
2. Scroll ke bawah, lihat **README.md preview**
3. **Verify** bahwa isi sudah berubah sesuai template
4. ✅ **DONE!**

---

```
1. Repository page → Click "README.md" file
   [File: README.md] ← Click this

2. File view → Click pencil icon
   [✏️ Edit this file] ← Click this

3. Editor → Select all, delete, paste template
   [Editor with new content]

4. Scroll down → Commit message
   [Commit message: "Add complete documentation..."]
   
5. Click "Commit changes"
   [Commit changes] ← Click this green button

6. Done! README updated ✅
```

---

## **⏱️ ESTIMATED TIME:**

- Total: **3-5 menit**
- Step 1-3: 1 menit
- Step 4: 1 menit (copy-paste)
- Step 5-7: 1-2 menit

---

## **✅ CHECKLIST:**
```
[ ] 1. Buka repository di GitHub
[ ] 2. Click README.md file
[ ] 3. Click pencil icon (edit)
[ ] 4. Select All → Delete → Paste template
[ ] 5. Preview (optional)
[ ] 6. Add commit message
[ ] 7. Click "Commit changes"
[ ] 8. Verify changes appear
```

---

## **🎯 READY TO START?**

**Buka sekarang:**
```
https://github.com/Budiman002/a428-cicd-labs
