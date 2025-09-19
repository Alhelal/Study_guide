Jenkins Notes:
================

1.  **Find a easy , and simple solution to backup all the Jenkins instances in your environment . It needs to be cost effective , but the main focus is it being simple , and easy to implement.**

    **Answer:**
        Simply backup the JENKINS_HOME directory or /var/jenkins_home for Docker images. It has enough information to restore Jenkins to a new compute instance .
        The JENKINS_HOME directory contains all necessary configurations, plugins, job definitions, build history, and other critical data needed to restore a Jenkins instance, making it a simple and effective backup solution.

2.  **You have a distributed build team, with developers across the spectrum working on a variety of topics. This sometimes resulting in confusion as to what artifact output from one build pipeline is being used/leveraged in the subsequent test/build/release pipeline. What can be used to ensure that you can keep a track of what version of a file/artifact is being used in a build/pipeline job?**

    **Answer:**
        You need to fingerprint the artifacts. Then you can very easily understand what version of your artifact is being leveraged in what build number and so, effectively allowing dependency tracking in your system.

3.  **Why should you not run builds in Jenkins master node?**

    **Anwser:**
        Running builds directly on the Jenkins master node (now referred to as the controller or built-in node) is generally discouraged due to several key reasons:
            *Security Risks:* \
                * Builds executed on the controller have unrestricted access to the JENKINS_HOME directory and the controller's file system. This poses a significant security vulnerability, as a compromised or malicious build could potentially access sensitive data like credentials, modify configurations, or even install malicious plugins, impacting the entire Jenkins installation. \               
                * If different users have varying levels of access (e.g., some can configure jobs but not administer Jenkins), running builds on the controller can bypass these restrictions and grant unauthorized access to critical resources.

            Performance* and Scalability Issues:
                
                * The controller's primary role is to manage the Jenkins environment, handle HTTP requests, and orchestrate builds. Running computationally intensive builds directly on the controller can consume its resources, leading to performance degradation, slow UI responsiveness, and potential instability of the Jenkins instance itself.
                
                *This approach limits the scalability of your CI/CD pipeline. As the number of projects and builds grows, the controller can become a bottleneck, making it difficult to handle increased workload efficiently.
            
            Resource Contention:
                
                * Running builds on the controller can lead to resource contention between the Jenkins core processes and the build processes, potentially impacting the stability and reliability of both.