# AMQ-Streams Operator Deployment

## Description

This code base is ingested by ArgoCD/Red Hat Git Ops to deploy and manage the AMQ-Streams Operator into an OpenShift cluster using GitOps.

The code is structured using kustomize to template the included yaml and allow customized deployment to multiple environemnts including Staging, Development, and Production

This code can also be used to manage the version or upgarde of the AMQ-Streams operator installed and ensure consistancy across all clusters in a particular environment category.

## Installation

To utilize this project setup a new "Application" within your ArgoCD Instance that has access to this repository. Target the appropriate overlay by providing the path. This should match the environment you are deploying the operator too. Use overlay/Stagining for Staging overlay/Development for Development and overlay/Production for production.

You can verify what versions will be targetted for initial installation and the final upgraded version by checking the operator.versions file within each of the overlay folders

Once the application is created within ArgoCD use the Sync actions to advance the configuration.

Sync 1 - Installs the Operator Subscription into the target Namespace and generates an InstallPlan to do the actual operator installation  
Sync 2- Approves the initial InstallPlan and allows the cluster to finish the initial installation of the Operator into the target namespace  
Sync 3- Approves any generated InstallPlans for upgrading the Operator to the version that is found the operator.version file and allows that upgrade to complete  
  
Your Operator is now installed, upgraded, and ready for use.  

## Usage

This code is leveraged in a GitOps fashion by ArgoCD so any changes to configuration or capability are done directly within the Git Repository. Here are some potential configuration changes and how to action them:
  
Changing Install Version: in the appropriate overlay (staging, development, production) update the operator.version file to have the new initial version you wish to install. Also, update the subscription objects desiredCSV field to have the new initial version.  

Changing the Upgrade Version: in the appropriate overlay (staging, development, production) update the operator.version file to have the new upgrade version you wish to install. Run the "sync" operation and any install plans available for that version will be approved and the upgrade will roll out.  


## Features

This code works by leveraging 2 OpenShift Jobs to approve InstallPlans for when Operators are set to "Manual" Approval mode. These Jobs leverage a serviceaccount within the cluster to check for install plans that match the versions in the operator.version file and appove them as needed. Once approved OpenShift handles migrating the Opeartor to the version approved in the InstallPlan  
