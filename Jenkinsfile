pipeline {
  agent any

  options {
    timestamps()
    ansiColor('xterm')
  }

  stages {
    stage('Checkout') {
      steps {
        // SSH로 접근: git@github.com:kyh0304/jenkins-git-test.git
        checkout([$class: 'GitSCM',
          branches: [[name: '*/master']],
          userRemoteConfigs: [[url: 'git@github.com:kyh0304/jenkins-git-test.git']]
        ])
        sh 'echo "GIT_COMMIT: $(git rev-parse --short HEAD)"'
      }
    }

    stage('Build') {
      steps {
        sh '''
          echo "[Build] 프로젝트 빌드 흉내만 냅니다."
          mkdir -p build && echo "built at $(date)" > build/info.txt
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          echo "[Test] 간단한 테스트 실행"
          test -f version.txt && echo "version.txt OK" || { echo "version.txt 없음"; exit 1; }
        '''
      }
    }

    stage('Package') {
      steps {
        sh '''
          VER=$(cat version.txt 2>/dev/null || echo "0.0.0")
          SHA=$(git rev-parse --short HEAD)
          mkdir -p dist
          tar -czf dist/app-${VER}-${SHA}.tar.gz app.py version.txt build || true
          echo "생성물: dist/app-${VER}-${SHA}.tar.gz"
        '''
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'dist/*.tar.gz', fingerprint: true, onlyIfSuccessful: true
      echo '✅ 성공: 아티팩트가 보관되었습니다.'
    }
    failure {
      echo '❌ 실패: Console Output을 확인하세요.'
    }
    always {
      echo "작업 완료: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
    }
  }
}
