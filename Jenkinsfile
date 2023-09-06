def cacheOrRestoreNodeModules() {
    if (params.CACHED_NODE_MODULES) {
        sh '''        
        MD5_SUM_PACKAGE_JSON=($(md5sum package.json))
        # CACHE_FOLDER=/home/jenkins/.cache/npm/${MD5_SUM_PACKAGE_JSON}
        CACHE_FOLDER=/var/lib/jenkins/workspace/jenkins-nodejs/.cache/npm/${MD5_SUM_PACKAGE_JSON}
        
        # check if folder exists and copy node_modules to current directory
        if [ -d ${CACHE_FOLDER} ]; then
          cp -r ${CACHE_FOLDER}/node_modules .
        fi
        
        npm install --no-audit
        
        # if folder does not exists, create it and cache node_modules folder
        if ! [ -d ${CACHE_FOLDER} ]; then
          mkdir -p ${CACHE_FOLDER}
          cp -r node_modules ${CACHE_FOLDER}/node_modules
        fi
        '''
    }
}

pipeline {
    // agent {
    //     docker { image 'node:18.17.1-alpine3.18' }
    // }

    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000'
        }
    }

    parameters {
        booleanParam(name: "CACHED_NODE_MODULES",
                description: "Should node_modules be taken from cache?",
                defaultValue: !'master'.equals(env.BRANCH_NAME) && !'develop'.equals(env.BRANCH_NAME))
    }

    stages {

        stage('Test') {
            steps {
                sh 'node --version'
            }
        }

        // stage('Build') {
        //     steps {
        //         // cacheOrRestoreNodeModules()
        //         // sh 'npm install'

        //         sh 'npm i'

        //         sh 'tar cvfz ${HOME}/jenkins-nodejs.tar.gz node_modules'

        //         // sh 'zip -r jenkins-home-backup /var/lib/jenkins -x /var/lib/jenkins/caches/\*'

        //     }
        // }

	stage('Git') {
        steps {        
		    git 'https://github.com/claudio-bianco/jenkins-nodejs'
        }
	}

	stage('Build') {
        steps {           
		    sh 'npm install'	
        }        
    }
}
}