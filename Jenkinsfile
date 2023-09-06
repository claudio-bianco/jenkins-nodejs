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

    environment {
        // NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
        // NPM_CONFIG_CACHE = "${WORKSPACE}/src/node_modules"
        HOME = '.'
    }

    stages {

        // stage('Test') {
        //     steps {
        //         sh 'node --version'
        //     }
        // }

        // stage('Build') {
        //     steps {
        //         // cacheOrRestoreNodeModules()
        //         // sh 'npm install'

        //         sh 'npm i'

        //         sh 'tar cvfz ${HOME}/jenkins-nodejs.tar.gz node_modules'

        //         // sh 'zip -r jenkins-home-backup /var/lib/jenkins -x /var/lib/jenkins/caches/\*'

        //     }
        // }

	// stage('Git') {
    //     steps {        
	// 	    git 'https://github.com/claudio-bianco/jenkins-nodejs'
    //     }
	// }

	// stage('Build') {
    //     steps {         
    //         // cacheOrRestoreNodeModules()
    //         // sh 'cd src && ls -la'
	// 	    sh 'cd src && npm install'	
    //     }        
    // }

    stage('NPM Build') {
        steps {
            sh '''
            MD5_SUM_PACKAGE_JSON=$(set -- $(md5sum src/package.json); echo $1)
            CACHE_FOLDER=${WORKSPACE}/.cache/npm/${MD5_SUM_PACKAGE_JSON}
            
            # check if folder exists and copy node_modules to current directory
            if [ -d ${CACHE_FOLDER} ]; then
            cp -r ${CACHE_FOLDER}/node_modules ./src
            fi
            
            cd src && npm install --no-audit
            
            # if folder does not exists, create it and cache node_modules folder
            if ! [ -d ${CACHE_FOLDER} ]; then
            mkdir -p ${CACHE_FOLDER}
            cp -r src/node_modules ${CACHE_FOLDER}/node_modules
            fi
            '''
        }
    }
    
  }
}