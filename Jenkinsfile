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
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
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

	// stage('Git') {
    //     steps {        
	// 	    git 'https://github.com/claudio-bianco/jenkins-nodejs'
    //     }
	// }

	// stage('Build') {
    //     steps {         
    //         cacheOrRestoreNodeModules()  
	// 	    sh 'npm install'	
    //     }        
    // }

    stage('NPM Build') {
        steps {
            sh '''
            node -v && npm -v
            '''
            // rm -rf node_modules
            sh '''
            CACHE_FOLDER=${HOME}/.cache/md5
            echo "EXECUTOR_NUMBER: ${EXECUTOR_NUMBER}"
            MD5_FILE_NAME=package-json_${EXECUTOR_NUMBER}.md5sum

            [ -d ${CACHE_FOLDER} ] || mkdir -p ${CACHE_FOLDER}
            ls ${CACHE_FOLDER}

            if [ -f ${CACHE_FOLDER}/${MD5_FILE_NAME} ];then
            cp ${CACHE_FOLDER}/${MD5_FILE_NAME} ${MD5_FILE_NAME}
            md5sum package.json
            cat ${MD5_FILE_NAME}
            md5sum -c ${MD5_FILE_NAME} || npm ci
            else
            echo "No md5sum backup"
            npm ci
            fi

            echo "create new md5sum backup"
            md5sum package.json
            md5sum package.json > ${MD5_FILE_NAME}
            cp ${MD5_FILE_NAME} ${CACHE_FOLDER}
            '''
            sh '''
            npm run ngcc
            '''
            sh '''
            npm run build
            '''
        }
    }
    
}
}