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

Repo commands

    helm repo list

    helm repo add bitnami https://charts.bitnami.com/bitnami

    helm repo remove bitnami

To update local cache of repo for searching:

    helm repo update 

Search the repository:

    helm search repo mysql

    helm search repo mysql --versions


Install a package:

    helm install mydb bitnami/mysql

To check the installation status:

    helm status mydb

To list installations:

    helm list


To install with different parameters:

    helm install mydb bitnami/mysql --values <values.yaml path>

To generate yaml without validating:

    helm template mydb bitnami/mysql --values <values.yaml path>


To download helm chart tgz:

    helm pull firstchart localRepo/firtschart

To install from a local tgz:

    helm install firstchart firstchart-0.1.0.tgz
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
Syntax Related:

    {{}} is used for dynamic code creation
    "-" eliminates spaces that may cause problem 
    . points to root
    .Release for release information
    | gets output of previous statement and use it as input 
    and, or used just after if
    range for looping
    include is used to invoke functions which can be used with pipe unlike template
    import-values field can be used to import exported values from dependency charts. Unexported values can be imported using child and map with parent.
    hooks can be used to run before and after installations.
    tags is used to conditionally add multiple dependencies when are using the same condition
    hook-weight is used to prioritize hooks

To add dependency charts:

    helm dependency update <chartname>

To create a repo index under charsrepo directory: 

    helm repo index chartsrepo/ 
    helm package firstchart -d chartsrepo


## OCI Registry
    docker run -d --name oci-registry -p 5000:5000 registry

    helm package firstchart

    helm push firstchart-0.1.0.tgz oci://localhost:5000/helm-charts

    helm show all oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm pull oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm template myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm install myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0

    helm upgrade myrelease oci://localhost:5000/helm-charts/firstchart --version 0.2.0

    helm registry login -u myuser <oci registry>

    helm registry logout <oci registry url>

## security
    gnu gp download 
    gpg --full-generate-key ( to create key) 
    gpg --export-secret-keys | ~/.gnupg/secring.gpg (to export them to secring.gpg file instead of kbx) 
    helm package --sign --key <email> --keyring ~/.gnupg/secring.gpg firstchart -d chartsrepo (will create a prov  file which has alias(email) as well) 
    helm verify chartsrepo/firstchart-0.1.0.tgz --keyring  ~/.gnupg/secring.gpg (to verify) 
    helm install --verify --keyring  ~/.gnupg/secring.gpg  localrepo/firstchart

## Starters
To create a start and use it

    helm env HELM_DATA_HOME (to get the home directory) 
    Create a home/starters folder 
    copy the chart and replace chartname with <CHARTNAME> in files under template 
    helm create --starter <templatename> <customappname>

## Plugins
    helm plugin list
    helm plugin install https://github.com/salesforce/helm-starter.git (or tar ball)
    helm <plugin name> --help
    helm plugin update <plugin name>
    helm plugin remove <plugin name>
    with platformCommand diff commands can be defined for each os


## More Helm
    values.schema.json is used to validate values 
    helm lint . (to validate)

    To generate schema 
        convert values from yaml to json- (https://www.json2yaml.com) \
        convert json to schema (https://jsonschema.net/home) 