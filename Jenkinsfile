pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        CARGO_TERM_COLOR = 'always'
        CI = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Versions') {
            steps {
                sh '''
                    rustc --version
                    cargo --version
                    bun --version
                '''
            }
        }

        stage('Install Frontend') {
            steps {
                sh 'bun install --frozen-lockfile'
            }
        }

        stage('Rust Format') {
            steps {
                sh '''
                    cargo fmt \
                        --manifest-path src-tauri/Cargo.toml \
                        --all \
                        -- \
                        --check
                '''
            }
        }

        stage('Rust Clippy') {
            steps {
                sh '''
                    cargo clippy \
                        --manifest-path src-tauri/Cargo.toml \
                        --all-targets \
                        --all-features \
                        -- \
                        -D warnings
                '''
            }
        }

        stage('Rust Tests') {
            steps {
                sh '''
                    cargo test \
                        --manifest-path src-tauri/Cargo.toml \
                        --all-features
                '''
            }
        }

        stage('Frontend Build') {
            steps {
                sh 'bun run build'
            }
        }

        stage('Tauri Build') {
            steps {
                sh 'bun run tauri build'
            }
        }
    }

    post {
        always {
            archiveArtifacts(
                artifacts: 'src-tauri/target/release/bundle/**/*',
                allowEmptyArchive: true
            )
        }
    }
}
