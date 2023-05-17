# It is a package manager for kubernetes World
It works with charts(like packages). A chart will have all the template files and the configuration required to create Kubernetes resources. It uses go syntax.

For every version values are recorded as secrets

### Pros
 - Simplifies the installation with a single command
 - Can be dynamic with parameters (values.yaml)
 - Revision History
 - Consistency 
 - intelligent enough to run the yamls in correct order
 - security (checks signatures and hashes)


## WORKING STEPS
    Load the chart and its dependencies
    Parse the values.yaml
    Generate the yaml
    Parse the YAML to kube objects and validate
    Generate YAML and Send to Kube (with --dry-run parameter it does not send to kube )

# Commands

helm repo list

helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo list

helm repo remove bitnami

helm repo add bitnami https://charts.bitnami.com/bitnami


Search the repository:

    helm search repo mysql

    helm search repo database

    helm search repo database --versions


Install a package:

kubectl get pods

(Below Two commands - In a Different Shell/Commandline window/tab)

minikube ssh

docker images

helm install mydb bitnami/mysql

Check the cluster:

kubectl get pods

minikube ssh

docker images

To check the installation status:

    helm status mydb

To list installations:

    helm list


To install with different parameters:

    helm install mydb bitnami/mysql --values <values.yaml path>

To generate yaml without validating:

    helm template mydb bitnami/mysql --values <values.yaml path>

--------------------------------------------

To Upgrade:

    ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)

    helm upgrade --namespace default mysql-release bitnami/mysql --set auth.rootPassword=$ROOT_PASSWORD 
        --install parameter can be used to install if not already installed 
        --generate-name parameter can be used to generate a name automatically. 
            Ex: --generate-name --name-template "mydb-{{randAlpha 7| lower}}"
        --wait --timeout 5m10s to wait instance to be up and running otherwise it will be marked as failed. Default time out 5 minutes
        --atomic to rollback to a previous successful revision in case of failure timeouts
        --force deletes and installs new instances when upgrading
        --cleanup-on-failure deletes all objects(configmaps, secrets) when upgrade fails



-------

helm uninstall mysql-release (--keep-history (to keep secrets and history after uninstall))

helm get notes mydb

helm get values mydb

    --all for getting default values as well
    --revision 1 for getting revision 1)

helm get manifest mydb

helm history mydb (to see revisions and their statuses)

helm rollback mydb 1 (adds another revision) 

To create a new chart:

    helm create firstchart (uses nginx chart as template)

To package your chart:

    helm package firstchart
        --dependencies-update or -u can be used to package other charts that our chart depends on
        -d or --destination to specify the output directory


 To check if any syntax issue exists:

    helm lint firstchart


# Templates
    {{}} is used for dynamic code creation
    "-" eliminates spaces that may cause problem 
    . points to root
    .Release for release information
    | gets output of previous statement and use it as input 
    and, or used just after if
    range for looping
    include is used to invoke functions which can be used with pipe unlike template


