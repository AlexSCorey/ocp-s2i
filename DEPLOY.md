# Quick S2I Deployment Guide

## 🚀 Deploy Frontend using Source-to-Image

### Method 1: From Local Directory (Fastest for demo)

```bash
# Navigate to frontend directory
cd frontend

# Create a new build using nginx S2I builder
oc new-build nginx:latest --name=todo-frontend --binary=true

# Start the build from current directory
oc start-build todo-frontend --from-dir=. --follow

# Create the application from the built image
oc new-app todo-frontend

# Expose the service to create a route
oc expose svc/todo-frontend

# Get the URL
oc get route todo-frontend
```

### Method 2: From Git Repository

First, push the frontend folder to a Git repository, then:

```bash
# Deploy directly from Git
oc new-app nginx:latest~https://github.com/YOUR-USERNAME/YOUR-REPO.git \
  --context-dir=frontend \
  --name=todo-frontend

# Expose the service
oc expose svc/todo-frontend

# Get the URL
oc get route todo-frontend
```

### Method 3: Using the YAML file

```bash
# Apply the S2I configuration
oc apply -f openshift-s2i.yaml

# Trigger the build (if using Git)
oc start-build todo-frontend --follow
```

## 🔧 Verify Deployment

```bash
# Check build status
oc get builds

# Check if pod is running
oc get pods -l app=todo-frontend

# Check logs
oc logs -f deployment/todo-frontend

# Get the route URL
echo "Frontend URL: http://$(oc get route todo-frontend -o jsonpath='{.spec.host}')"
```

## 🌐 Access the Application

```bash
# Open in browser
oc get route todo-frontend
# Copy the URL and open in browser
```

## 🎯 What is S2I?

**Source-to-Image (S2I)** is an OpenShift feature that:
- Takes source code as input
- Uses a builder image (nginx in our case)
- Produces a ready-to-run container image
- No Dockerfile needed!

### Benefits:
- ✅ No need to write Dockerfiles
- ✅ Standardized build process
- ✅ Automatic security updates
- ✅ Faster builds with layer caching
- ✅ Git webhook integration

## 📊 Complete Architecture

```
┌─────────────────┐
│   User Browser  │
└────────┬────────┘
         │
         ▼
┌─────────────────────┐
│  todo-frontend      │
│  (nginx S2I)        │
│  - Serves HTML      │
│  - Proxies /api     │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  todo-api           │
│  (Python Flask)     │
│  - REST API         │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  postgresql         │
│  (Database)         │
└─────────────────────┘
```

## 🔄 Update the Frontend

```bash
# Make changes to index.html
# Then rebuild:
oc start-build todo-frontend --from-dir=. --follow

# Or if using Git:
git push
# Build triggers automatically via webhook
```

## 🐛 Troubleshooting

```bash
# Build failed?
oc logs -f bc/todo-frontend

# Pod not starting?
oc describe pod -l app=todo-frontend

# Check events
oc get events --sort-by='.lastTimestamp' | grep todo-frontend

# API connection issues?
oc exec deployment/todo-frontend -- curl -v http://todo-api:5000/
```

## 🎓 Key Concepts for Your Presentation

1. **S2I vs Dockerfile**: Show both approaches
2. **Builder Images**: nginx, nodejs, python, etc.
3. **Binary Builds**: Build from local directory
4. **Git Builds**: Automatic from repository
5. **Triggers**: ConfigChange, ImageChange, Webhook

---

Perfect for demonstrating OpenShift's developer-friendly features!
