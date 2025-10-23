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
