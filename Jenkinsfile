def cacheOrRestoreNodeModules() {
    if (params.CACHED_NODE_MODULES) {
        sh '''
        MD5_SUM_PACKAGE_JSON=$(set -- $(md5sum package.json); echo $1)
        CACHE_FOLDER=${WORKSPACE}/.cache5/npm/${MD5_SUM_PACKAGE_JSON}            
        
        # check if folder exists and copy node_modules to current directory
        if [ -d ${CACHE_FOLDER} ]; then
            cp -r ${CACHE_FOLDER}/node_modules .
        fi
        
        # cd src && npm install --no-audit
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
    agent {
        docker {
            image 'node:8.12.0'
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
        npm_config_cache = 'npm-cache'
        HOME = '.'
        // // Override HOME to WORKSPACE value
        // HOME = "${WORKSPACE}"
        // // or override npm's cache directory (~/.npm)
        // NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
    }

    stages {

    stage('Clone') {
        steps {
            git branch: 'main',
                // credentialsId: '1234',
                url: 'https://github.com/claudio-bianco/jenkins-nodejs'
        }
    }

	stage('Build') {
        steps {         
            cacheOrRestoreNodeModules()
            // sh 'cd src && ls -la'
		    // sh 'npm install'
        }        
    }

    // stage('NPM Build') {
    //     steps {
    //         sh '''
    //         MD5_SUM_PACKAGE_JSON=$(set -- $(md5sum package.json); echo $1)
    //         CACHE_FOLDER=${WORKSPACE}/.cache5/npm/${MD5_SUM_PACKAGE_JSON}            
            
    //         # check if folder exists and copy node_modules to current directory
    //         if [ -d ${CACHE_FOLDER} ]; then
    //             cp -r ${CACHE_FOLDER}/node_modules .
    //         fi
            
    //         # cd src && npm install --no-audit
    //         npm install --no-audit
            
    //         # if folder does not exists, create it and cache node_modules folder
    //         if ! [ -d ${CACHE_FOLDER} ]; then
    //             mkdir -p ${CACHE_FOLDER}
    //             cp -r node_modules ${CACHE_FOLDER}/node_modules
    //         fi
    //         '''
    //     }
    // }
    
  }
}