Create a public Git repository with your homework code.
MitziCom has a shared Nexus deployed in the cluster using the service nexus3.gpte-hw-cicd.svc.cluster.local:8081/repository/all-maven-public
  The User ID to access Nexus repositories and container registries is admin with password redhat.

MitziCom has a Shared SonarQube installation deployed in the Cluster using the service sonarqube.gpte-hw-cicd.svc.cluster.local:9000.
  The UserID for SonarQube is also admin with password redhat - although you should not need to use credentials to analyze projects.

GUID=4221
Create a persistent Jenkins in project ${guid}-jenkins.
oc new-project '4221-jenkins'
oc new-app --template=jenkins-persistent --param=MEMORY_LIMIT=2Gi --param=VOLUME_CAPACITY=4Gi --param=DISABLE_ADMINISTRATIVE_MONITORS=true -n 4221-jenkins

Create a custom agent image (which includes skopeo) to build your pipeline.
    The agent image must have the name jenkins-agent-appdev in project ${guid}-jenkins.
oc new-build  -D $'FROM docker.io/openshift/jenkins-agent-maven-35-centos7:v3.11\n
      USER root\nRUN yum -y install skopeo && yum clean all\n
      USER 1001' --name=jenkins-agent-appdev -n ${guid}-jenkins


./setup_projects.sh 4221 fabian.dammekens-cegeka.com true
./setup_dev.sh 4221
./setup_jenkins.sh 4221 https://github.com/fdammeke/advdev_homework_template.git na311.openshift.opentlc.com

oc create -f buidconfig.yaml -n 4221-jenkins
