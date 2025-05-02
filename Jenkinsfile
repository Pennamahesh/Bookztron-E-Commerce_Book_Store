pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git credentialsId: 'git-cred', branch: 'main', url: 'https://github.com/Pennamahesh/Bookztron-E-Commerce_Book_Store.git'
            }
        }
        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "maheshpenna96/book-e-commarce:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t ${DOCKER_IMAGE} .'

                    // Push the Docker image to DockerHub
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        dockerImage.push()  
                    }
                }
            }
        }
        stage('Update Deployment File') {
            environment {
                GIT_REPO_NAME = "Bookztron-E-Commerce_Book_Store"
                GIT_USER_NAME = "Pennamahesh"
            }
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                        git config user.email "pennamahesh2001@gmail.com"
                        git config user.name "Pennamahesh"
                        
                        # Replace image tag in deployment.yml
                        sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" deployment.yaml
                        
                        # Commit the change and push to GitHub
                        git add deployment.yaml
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
}



/*
sed -i "s|\(image: .*:\).*|\1${BUILD_NUMBER}|" deployment.yaml
🔍 Step-by-step Explanation:
1. sed -i
-i = inline edit (modifies the file directly).

2. s|...|...|
sed substitution syntax.
We use | instead of / to avoid conflicts with / in image names (e.g., myrepo/app).

3. Search Pattern: \(image: .*:\).*
image: — matches the literal string "image:"

.*: — matches any characters (image name) followed by a colon (:)

\(...\) — captures the above as group 1

.* — matches the tag (which we want to replace)

Example matched line:

t
image: myrepo/myapp:oldtag
Group 1 (captured):


image: myrepo/myapp:
4. Replacement: \1${BUILD_NUMBER}
\1 — refers to the captured group 1 (image: myrepo/myapp:)

${BUILD_NUMBER} — inserts the Jenkins build number (e.g., 24)

So the line becomes:


image: myrepo/myapp:24
Example Before and After:
Before:


image: myregistry/myapp:latest
After Jenkins build 42:


image: myregistry/myapp:42

*/
