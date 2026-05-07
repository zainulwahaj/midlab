pipeline {
      agent any

      options {
          disableConcurrentBuilds()
          timestamps()
      }

      stages {
          stage('Fetch Data From GitHub') {
              steps {
                  checkout scm
              }
          }

          stage('Train Model') {
              steps {
                  sh '''
                      python3 -m venv .venv
                      . .venv/bin/activate
                      pip install --upgrade pip
                      pip install -r requirements.txt
                      python train.py
                  '''
              }
          }

          stage('Build Docker Image') {
              steps {
                  sh 'docker build -t ml-midterm-api:latest .'
              }
          }

          stage('Run Docker Container') {
              steps {
                  sh '''
                      docker rm -f ml-midterm-api || true
                      docker run -d \
                        --name ml-midterm-api \
                        --restart unless-stopped \
                        -p 8000:8000 \
                        ml-midterm-api:latest
                  '''
              }
          }

          stage('Verify Metrics API') {
              steps {
                  sh '''
                      sleep 3
                      curl -f http://localhost:8000/metrics
                  '''
              }
          }
      }
  }