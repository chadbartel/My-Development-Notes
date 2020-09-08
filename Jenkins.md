# Jenkins

## Helpful links

* [Documentation](https://www.jenkins.io/doc/)
* [Getting started](https://www.jenkins.io/doc/pipeline/tour/getting-started/)
* [Tutorials](https://www.jenkins.io/doc/tutorials/)
* [Jenkins glossary](https://www.jenkins.io/doc/book/glossary/)
* [Jenkins pipeline syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)

## Projects

* [Set up a Jenkins build server](https://d1.awsstatic.com/Projects/P5505030/aws-project_Jenkins-build-server.pdf)
* [Setting up a CI/CD pipeline by integrating Jenkins with AWS CodeBuild and AWS CodeDeploy](https://aws.amazon.com/blogs/devops/setting-up-a-ci-cd-pipeline-by-integrating-jenkins-with-aws-codebuild-and-aws-codedeploy/)

## Summary

> Jenkins is a self-contained, open source automation server which can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software.

## [Set Up a Jenkins Build Server](https://d1.awsstatic.com/Projects/P5505030/aws-project_Jenkins-build-server.pdf)

 1. Prerequisites

    * [Create and set up an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)
    * [Create an IAM user and password in your AWS account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
    * [Create an Amazon EC2 key pair](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-keypairs.html)
    * [Create and configure a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-getting-started.html)

    > Before continuing in the guide, navigate to the [Amazon EC2 console](https://console.aws.amazon.com/ec2/).

 2. Launch an EC2 Instance

    * Create a [security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html) for your Amazon EC2 instance
        1. Decide how your Jenkins server should be access and by whom:

            * Identify the CIDR block that should have acces to the instance via HTTP (or leave it open, i.e. `0.0.0.0/0`)
            * Identify who should have SSH access to the instance ([your IP address](http://checkip.amazonaws.com/), `0.0.0.0/0`, etc.)
            * Record these values so we can use them when we create the security group

        2. [Open the Amazon EC2 console](https://console.aws.amazon.com/ec2/)
        3. Make sure you select the region you want (e.g. **US West (Oregon)**)
        4. On the left, select **Security Groups** and then click **Create Security Group**
        5. Give the security group an appropriate name, like `WebServerSG`
        6. Choose your VPC from the list
        7. Select the **Inbound** tab and add the rules:
            * Click **Add Rule**, select **SSH** as the **Type**
                * Under **Source**, select **Custom** and enter the IP range from step 1
            * Click **Add Rule**, and then choose **HTTP** as the **Type**
                * Under **Source**, select **Custom** and enter the IP range from step 1
            * Click **Add Rule**, and then choose **Custom TCP Rule** as the **Type**
                * Under **Port Range** enter `8080` 
                * Under **Source**, select **Custom** and enter the IP range from step 1
            * Click **Create**
    * Launch an EC2 instance
        1. On the left, select **Instances** and then click **Launch Instance**
        2. Select an Amazon Linux AMI with HVM virtualization as the **Amazon Machine Image**
        3. Select a free instance type, like `t2.micro`, on the **Choose an Instance Type** page
        4. On the **Configure Instance Details** page:
            * From **Network**, select your VPC and one of your *public* subnets
            * Select **Enable** under the **Auto-assign Public IP** option
        5. Continue through the EC2 configuration until you reach the **Configure Security Group** page:
            * Click the **Select an existing security group** radio button
            * Select the `WebServerSG` security group you created earlier
        6. Click **Review and Launch**
        7. Review that all the details for your EC2 look correct
        8. Click **Launch**
        9. In the **Select an existing key pair or create a new key pair** dialog box, select the key pair you created in the *Prerequisites* step earlier
        10. Check the acknowledgement box and click **Launch Instances**
 3. Install and Configure Jenkins
    * Connect to your Linux instance
        1. Using whatever method you would like (e.g. PuTTY, bash, Powershell) with the private key file you created from the *Prerequisites* step earlier, connect to your Linux EC2 instance, for example:

            ```powershell
            PS C:\Users\YourUsername> ssh -i /path/to/key.pem ubuntu@instance-id
            ```

        2. It will prompt you to confirm if you want to connect to the instance by displaying the RSA key fingerprint of the key pair, enter `yes`
        3. After confirming, it should say something about adding the DNS you connected to the list of known hosts
    * Download and install Jenkins
        1. Perform a software update:

            ```bash
            [ec2-user ~]$ sudo yum update -y
            ```

        2. Add the Jenkins repo:

            ```bash
            [ec2-user ~]$ sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
            ```

        3. Import a key file from Jenkins-CI:

            ```bash
            [ec2-user ~]$ sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
            ```

        4. Install Jenkins:

            ```bash
            [ec2-user ~]$ sudo yum install jenkins -y
            ```

        5. Start Jenkins service:

            ```bash
            [ec2-user ~]$ sudo service jenkins start
            ```

        6. If you get an error complaining about your Java version, you will probably have to perform a couple additional steps to install Java 8 or 11:

            * Java 8:

                ```bash
                [ec2-user ~]$ sudo yum install java-1.8.0-openjdk
                ```

            * Java 11:

                ```bash
                [ec2-user ~]$ sudo amazon-linux-extras install java-openjdk11
                ```

        7. Check which version of Java is active:

            ```bash
            [ec2-user ~]$ java -version
            ```

        8. Switch your version of java to the one that is compatible with Jenkins:

            ```bash
            [ec2-user ~]$ alternatives --config java
            ```

        9. After switching, confirm you have the correct one active:

            ```bash
            [ec2-user ~]$ java -version
            ```

        10. Try starting the Jenkins service again:

            ```bash
            [ec2-user ~]$ sudo service jenkins start
            ```

    * Configure Jenkins
        1. Connect to the Jenkins service using the public DNS [http://<your_server_public_DNS>:8080](http://<your_server_public_DNS>:8080) using a web browser
        2. Get the initial administrator password for Jenkins from your EC2 instance via the following command:

            ```bash
            [ec2-user ~]$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
            ```

        3. After you log in with the password above, you will be brought to the **Customize Jenkins** page where you will click **Install suggested plugins**
        4. After the installation completes, enter your *Administrator Credentials*, click **Save Credentials**, and then click **Start Using Jenkins**
        5. On the left, click **Manage Jenkins**, and then click **Manage Plugins**
        6. Click the **Available** tab and search for "`Amazon EC2 plugin`" (without quotes)
        7. Select the checkbox next to **Amazon EC2 plugin**, and then click **Install without restart**
        8. Once it's done installing, click **Go back to the top page**
        9. Again, click **Manage Jenkins**, and then **Configure System**
        10. Scroll to the bottom where it says **Cloud** (*you will probably have to follow a hyperlink to a new page, but the rest of the guide still applies*)
        11. Click **Add a new cloud**, and select **Amazon EC2**
        12. Fill out "*all*" the fields
            * Select **AWS Credentials** in the *Add Credentials* section

    > Now that you have created the example Jenkins server, we can clean up and destroy the resources to minimize charges to our AWS account.

 4. Clean Up
    * Delete your EC2 instance
        1. In the Amazon EC2 console, click **Instances**
        2. Select the instance you created for the Jenkins server and change the state to **Terminate**
