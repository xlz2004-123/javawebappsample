node {
    withEnv([
        'AZURE_SUBSCRIPTION_ID=b4efaca6-c923-4095-a946-8367fc289e9e',
        'AZURE_TENANT_ID=e22c355f-47a4-4291-a64e-dafe6fb59b22',
        'AZURE_RESOURCE_GROUP=jenkins-get-started-rg',
        'AZURE_WEBAPP_NAME=lingzhe-javawebapp-2026-1103'
    ]) {
        stage('Checkout') {
            checkout scm
        }

        stage('Build') {
            sh 'mvn -B clean package'
        }

        stage('Deploy') {
            withCredentials([
                usernamePassword(
                    credentialsId: 'AzureServicePrincipal',
                    usernameVariable: 'AZURE_CLIENT_ID',
                    passwordVariable: 'AZURE_CLIENT_SECRET'
                )
            ]) {
                sh '''
                    set +x
                    set -eu

                    test -f target/calculator-1.0.war

                    export AZURE_CONFIG_DIR="$(mktemp -d)"
                    trap 'rm -rf "$AZURE_CONFIG_DIR"' EXIT

                    az login --service-principal --username "$AZURE_CLIENT_ID" --password="$AZURE_CLIENT_SECRET" --tenant "$AZURE_TENANT_ID" --output none

                    az account set --subscription "$AZURE_SUBSCRIPTION_ID"

                    az webapp deploy --resource-group "$AZURE_RESOURCE_GROUP" --name "$AZURE_WEBAPP_NAME" --src-path target/calculator-1.0.war --type war --async false --timeout 600000
                '''
            }
        }
    }
}
