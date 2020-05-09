Jenkinsfile for Jenkins pipeline

#### Simple example for this project

```Jenkinsfile
pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Hello World"'
                 sh '''
                     echo "Multiline shell steps works too"
                     ls -lah
                 '''
             }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Upload to AWS') {
            steps {
                withAWS(region:'us-west-2', credentials:'aws-static') {
                    s3Upload(pathStyleAccessEnabled:true, payloadSigningEnabled: true, file:'index.html', bucket:'react-app-hosting-bucket')
                }
            }
        }
     }
}
```

#### Using docker to upload create-react-app

```
pipeline {
     agent {
        docker { image 'node:12.16.3-alpine3.11' }
     }
     environment {
        CI = 'true'
        HOME = '.'
    }
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Build Start"'
                 sh 'npm install --loglevel verbose'
                 sh 'npm run build'
             }
         }
         stage('Lint') {
              steps {
                  sh 'npm run lint'
              }
         }
         stage('Test') {
              steps {
                  sh 'npm test'
              }
         }
         stage('Upload to AWS') {
            steps {
                withAWS(region:'us-west-2', credentials:'aws-static') {
                    s3Upload(pathStyleAccessEnabled:true, payloadSigningEnabled: true, workingDir:'build', includePathPattern:'**/*', bucket:'react-app-hosting-bucket')
                }
            }
        }
     }
}
```
