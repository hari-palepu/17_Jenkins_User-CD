pipeline {
    agent {
        node {
            label 'Agent'
        }
    }

    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
        string(name: 'version', defaultValue: '', description: 'What is the artifact version?')
        string(name: 'environment', defaultValue: 'dev', description: 'What is environment?')
        booleanParam(name: 'Destroy', defaultValue: 'false', description: 'What is Destroy?')
        booleanParam(name: 'Create', defaultValue: 'false', description: 'What is Create?')
    }
    // build
    stages {
        stage('Print version') {
            steps {
                sh """
                    echo "version: ${params.version}"
                    echo "environment: ${params.environment}"
                """
            }
        }

        stage('Init') {
            steps {
                sh """
                    cd Terraform_tfvars
                    terraform init --backend-config=${params.environment}/backend.tf -reconfigure
                """
            }
        }

        stage('Plan') {
            steps {
                sh """
                    cd Terraform_tfvars
                    terraform plan -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}"
                """
            }
        }

        stage('Apply') {
            when{
                expression{
                    params.Create
                }
            }
            steps {
                sh """
                    cd Terraform_tfvars
                    terraform apply -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
                """
            }
        }

        stage('Destroy') {
            when{
                expression{
                    params.Destroy
                }
            }
            steps {
                sh """
                    cd Terraform_tfvars
                    terraform destroy -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
                """
            }
        }
        
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'this runs when pipeline is failed, used generally to send some alerts'
        }
        success{
            echo 'I will say Hello when pipeline is success'
        }
    }
}

//Run the below commnads in Terrafrom_tfvars folder to destroy
//terraform init -reconfigure -backend-config=dev/backend.tf
//terraform destroy -var-file=dev/dev.tfvars -var=app_version=1.0.0 -auto-approve
