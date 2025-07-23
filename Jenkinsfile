/*
 * Git Mirroring Pipeline
 *
 * Automated mirroring of git repositories between different hosts
 *
 * Author: 80h
 * GitHub: https://github.com/80h/git-mirroring-pipeline
 *
 * Configuration file format (repos_config.json):
 *
 * [
 *   {
 *     "name": "",                      // Repository name (human-readable identifier)
 *     "source": "",                    // Source repo SSH URL (format: git@host:repo.git)
 *     "source_credentials_id": "",     // Jenkins credentials ID for source repository
 *     "target": "",                    // Target repo SSH URL (format: git@host:repo.git)
 *     "target_credentials_id": ""      // Jenkins credentials ID for target repository
 *   },
 *   ...
 * ]
 */

def mirrorRepo(repo) {
    def logPrefix = "[${repo.name}]"

    try {
        echo "${logPrefix} 🚀 Starting mirroring: ${repo.source} → ${repo.target}"

        withCredentials([
            sshUserPrivateKey(credentialsId: repo.source_credentials_id, keyFileVariable: 'SOURCE_SSH_KEY'),
            sshUserPrivateKey(credentialsId: repo.target_credentials_id, keyFileVariable: 'TARGET_SSH_KEY')
        ]) {
            dir(repo.name) {
                def startTime = System.currentTimeMillis()

                // Clone source
                echo "${logPrefix} 🔄 Cloning source repository..."
                sh """
                    export GIT_SSH_COMMAND='ssh -o StrictHostKeyChecking=no -i $SOURCE_SSH_KEY'
                    git clone --mirror ${repo.source} .
                """

                // Configure and push to target
                echo "${logPrefix} 🔄 Configuring target and pushing changes..."
                sh """
                    export GIT_SSH_COMMAND='ssh -o StrictHostKeyChecking=no -i $TARGET_SSH_KEY'
                    git remote set-url --push origin ${repo.target}
                    git fetch -p origin
                    git push --mirror
                """

                def durationSec = (System.currentTimeMillis() - startTime) / 1000
                echo "${logPrefix} ✅ Successfully mirrored in ${durationSec} seconds"
            }
        }
    } catch (Exception e) {
        echo "${logPrefix} ❌ Failed to mirror: ${e.getMessage()}"
        error("Mirroring failed for ${repo.name}")
    }
}

def loadConfig() {
    try {
        echo "📂 Loading configuration file..."
        def configFile = readFile('repos_config.json')
        def config = readJSON(text: configFile)

        // Validate config
        if (!config || !config.every { repo ->
            repo.name && repo.source && repo.target &&
            repo.source_credentials_id && repo.target_credentials_id
        }) {
            error("❌ Invalid config: Each repo must have all required fields")
        }

        echo "✔ Loaded configuration for ${config.size()} repositories"
        return config
    } catch (Exception e) {
        error("❌ Failed to load configuration: ${e.getMessage()}")
    }
}

pipeline {
    agent any
    options {
        ansiColor('xterm')
        timestamps()
    }

    stages {
        stage('Load Config') {
            steps {
                script {
                    def config = loadConfig()
                    currentBuild.description = "Mirroring ${config.size()} repos"
                }
            }
        }

        stage('Mirror Repositories') {
            steps {
                script {
                    def config = loadConfig()
                    echo "🔄 Starting parallel mirroring of ${config.size()} repositories"

                    def results = parallel config.collectEntries { repo ->
                        ["Mirror ${repo.name}": {
                            mirrorRepo(repo)
                        }]
                    }
                }
            }
            post {
                always {
                    cleanWs()
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline failed. Check individual repository logs above."
        }
        success {
            echo "✅ All repositories processed (some might have failed but pipeline continued)"
        }
        unstable {
            echo "⚠ Some repositories failed to mirror (check logs above)"
        }
    }
}