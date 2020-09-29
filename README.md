
## Steps

Follow these steps to setup and run this code pattern. The steps are described in detail below.
1. [Get the code](#1-get-the-code)
2. [Create IBM Cloud Services](#2-create-ibm-cloud-services)
3. [Setup Hyperledger Fabric Network using IBM Blockchain Platform](#3-setup-hyperledger-fabric-network-using-ibm-blockchain-platform)

## 1. Get the code

- Clone the repo using the below command.
   ```
   git clone 
   ```
   
## 2. Create IBM Cloud Services

**Create IBM Kubernetes Service Instance**

Create a Kubernetes cluster with [Kubernetes Service](https://cloud.ibm.com/containers-kubernetes/catalog/cluster) using IBM Cloud Dashboard.

  ![Kubernetes Service](images/create_kubernetes_service.png)

  > Note: It can take up to 15-20 minutes for the cluster to be set up and provisioned.  

**Create IBM Blockchain Platform Service Instance**

Create [IBM Blockchain Platform Service](https://cloud.ibm.com/catalog/services/blockchain-platform) instance using IBM Cloud Dashboard.

![Blockchain Platform](images/create_IBP_service.png)

## 3. Setup Hyperledger Fabric Network using IBM Blockchain Platform

Follow this [tutorial](https://developer.ibm.com/tutorials/quick-start-guide-for-ibm-blockchain-platform/) to create fabric network using IBM Blockchain Platform. You can decide network components (number of organizations, number of peers in each org etc.) as per your requirement. For example, the blockchain network may consist of two organizations with single peer each and an orderer service for carrying out all the transactions.

This code pattern can be executed with the sample chaincode [fabcar.go](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar/go) or else you can install your own chaincode. Instantiate the chaincode after installation.
You can refer to step 12 to step 15 [here](https://developer.ibm.com/tutorials/quick-start-guide-for-ibm-blockchain-platform/) to install smart contract, instantiate and then download connection profile. The downloaded connection profile will be used in further steps.

## 4. Deploy this Spring boot appplication on IBM Cloud
a) Follow instructions to deploy from here https://www.ibm.com/cloud/architecture/toolchains/develop-kubernetes-app-toolchain
b) Provide GIT URL to toolchain as this application. (You may need to fork it if needed depending on the region you deploy on IBM Cloud) 

## 5. Register and Enrol User to connect to Hyperledger Fabric Network 
a) Install JDK11 on your local machine.
b) Download "connection profile" and save it under src/main/resources
c) Update src/main/resources/application.yml with name of "connection profile"
d) Run utility class "application.secret.wallet.util.EnrollAdminAndUser" with below arguments to Register and enrol Blockchain User as below
  
   mvn clean install
   mvn exec:java -Dexec.args="{connection profile file name} {admin user name} {admin user password} {user name} {user password}"
   #mvn exec:java -Dexec.args="org1msp_profile.json admin adminpw john johnpw"  

e) Update scripts/env_setup.yaml with output from step d
f) Connect to Kubernetes cluster via kubectl tool
g) Run below command to store Blockchain user's credentials as Kubernetes secrets
   ` kubectl apply -f ./env_setup.yaml`
h) Identity the "deployment name" of the deployed application by running below command. 
   `kubectl get deployment --all-namespaces`
   
i) Run below kubectl command to expose Kubernetes secrets as environment variables
    `kubectl set env --from=secret/wallet deployment/{Kubernetes name of the deployment}`  

## 6. Access the sample application
   You can now access the sample application by going to {APP URL}/swagger-ui.html