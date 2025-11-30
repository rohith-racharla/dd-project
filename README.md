In this project a full-stack MEAN (MongoDB, Express, Angular, Node.js) application is containerized with Docker and automatically deployed using Jenkins CI/CD pipeline.

Prerequisites:
1. AWS EC2 instance (Ubuntu)
2. Docker Engine, Docker Compose, and Docker Hub account for image registry
3. Jenkins for automated deploymend (CI/CD)

```
Project structure:

   dd-project/
├── docker-compose.yml              # Docker Compose configuration
├── nginx-proxy.conf                # Nginx reverse proxy config
├── Jenkinsfile                     # CI/CD pipeline definition
├── README.md
│
├── backend/                        # Node.js/Express backend
│   ├── Dockerfile
│   ├── package.json
│   ├── server.js
│   └── app/
│       ├── config/
│       │   └── db.config.js       # MongoDB connection config
│       ├── models/
│       │   └── tutorial.model.js
│       ├── controllers/
│       │   └── tutorial.controller.js
│       └── routes/
│           └── tutorial.routes.js
│
└── frontend/                       # Angular frontend
    ├── Dockerfile
    ├── nginx.conf                 # Frontend nginx config
    ├── package.json
    ├── angular.json
    └── src/
        └── app/
            ├── services/
            └── components/
```
