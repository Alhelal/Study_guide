Jenkins Notes:
================

1.  **Find a easy , and simple solution to backup all the Jenkins instances in your environment . It needs to be cost effective , but the main focus is it being simple , and easy to implement.**

    **Answer:**
        Simply backup the JENKINS_HOME directory or /var/jenkins_home for Docker images. It has enough information to restore Jenkins to a new compute instance .
        The JENKINS_HOME directory contains all necessary configurations, plugins, job definitions, build history, and other critical data needed to restore a Jenkins instance, making it a simple and effective backup solution.

2.  **You have a distributed build team, with developers across the spectrum working on a variety of topics. This sometimes resulting in confusion as to what artifact output from one build pipeline is being used/leveraged in the subsequent test/build/release pipeline. What can be used to ensure that you can keep a track of what version of a file/artifact is being used in a build/pipeline job?**

    **Answer:**
        You need to fingerprint the artifacts. Then you can very easily understand what version of your artifact is being leveraged in what build number and so, effectively allowing dependency tracking in your system.