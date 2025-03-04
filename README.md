# Jenkins Pipeline Script for Managing AWS EC2 Instances

This Jenkins pipeline script demonstrates how to securely manage AWS EC2 instances using the AWS CLI within Jenkins. It securely handles AWS credentials using Jenkins' built-in credentials management.

### Prerequisites:
1. **AWS CLI** must be installed on the Jenkins agents.
2. Set up **AWS IAM credentials** (access key and secret key) in Jenkins using the Credentials plugin.
3. Ensure that the **IAM role** or **user** associated with the access key has the necessary permissions to manage EC2 instances.

### Example Jenkins Pipeline Script:

```groovy
pipeline {
    agent any

    environment {
        // Use Jenkins credentials to securely fetch AWS access keys
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')  // Replace with your Jenkins credential ID
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')  // Replace with your Jenkins credential ID
        AWS_REGION = 'us-east-1'  // Replace with your preferred AWS region
    }

    stages {
        stage('Check EC2 Instance Status') {
            steps {
                script {
                    echo "Checking the status of EC2 instances..."

                    // Run the AWS CLI command to describe EC2 instances
                    def describeInstances = sh(script: '''
                        aws ec2 describe-instances \
                            --region ${AWS_REGION} \
                            --query 'Reservations[*].Instances[*].[InstanceId,State.Name]' \
                            --output table
                        ''', returnStdout: true).trim()

                    echo "EC2 Instances status:\n${describeInstances}"
                }
            }
        }

        stage('Start EC2 Instance') {
            steps {
                script {
                    echo "Starting EC2 instance..."

                    // Run the AWS CLI command to start a specific EC2 instance
                    def instanceId = 'i-xxxxxxxxxxxxxxxxx' // Replace with your EC2 instance ID
                    def startInstance = sh(script: '''
                        aws ec2 start-instances \
                            --instance-ids ${instanceId} \
                            --region ${AWS_REGION}
                        ''', returnStdout: true).trim()

                    echo "Started EC2 instance: ${startInstance}"
                }
            }
        }

        stage('Stop EC2 Instance') {
            steps {
                script {
                    echo "Stopping EC2 instance..."

                    // Run the AWS CLI command to stop a specific EC2 instance
                    def instanceId = 'i-xxxxxxxxxxxxxxxxx' // Replace with your EC2 instance ID
                    def stopInstance = sh(script: '''
                        aws ec2 stop-instances \
                            --instance-ids ${instanceId} \
                            --region ${AWS_REGION}
                        ''', returnStdout: true).trim()

                    echo "Stopped EC2 instance: ${stopInstance}"
                }
            }
        }

        stage('Terminate EC2 Instance') {
            steps {
                script {
                    echo "Terminating EC2 instance..."

                    // Run the AWS CLI command to terminate a specific EC2 instance
                    def instanceId = 'i-xxxxxxxxxxxxxxxxx' // Replace with your EC2 instance ID
                    def terminateInstance = sh(script: '''
                        aws ec2 terminate-instances \
                            --instance-ids ${instanceId} \
                            --region ${AWS_REGION}
                        ''', returnStdout: true).trim()

                    echo "Terminated EC2 instance: ${terminateInstance}"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline execution completed."
        }

        success {
            echo "EC2 instance management operations completed successfully."
        }

        failure {
            echo "There was an error during the EC2 instance management process."
        }
    }
}
```
## Explanation of Key Sections:
**Environment Variables:**

```AWS_ACCESS_KEY_ID``` and ```AWS_SECRET_ACCESS_KEY``` are securely retrieved using Jenkins' credentials() function. Replace ```'aws-access-key'``` and ```'aws-secret-key'``` with the IDs of your Jenkins credentials.
```AWS_REGION``` is set to the AWS region of your choice (e.g., us-east-1).
### Pipeline Stages:

- Check EC2 Instance Status: Retrieves the status of all EC2 instances in the specified region.
- Start EC2 Instance: Starts a specific EC2 instance by instance ID.
- Stop EC2 Instance: Stops a specific EC2 instance by instance ID.
- Terminate EC2 Instance: Terminates a specific EC2 instance by instance ID.
 ##  Post Actions:

Always print a message when the pipeline completes, with separate success and failure messages.

## Notes:
Ensure you replace the instance IDs (i-xxxxxxxxxxxxxxxxx) with actual ```EC2``` instance IDs.
This pipeline assumes that your IAM credentials have sufficient permissions to perform EC2 operations.
The script uses ```AWS CLI commands``` (describe-instances, start-instances, stop-instances, terminate-instances), which must be installed and properly configured in your Jenkins environment.
