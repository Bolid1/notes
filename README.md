# HowTo topics in my life

## Set header in `.md` files
For example, you have the header:
```markdown
## My best header
My text
```
If you wan't to link to the header in another part of document,
just replace all spaces in header to `-`. For example:
```markdown
[my link](#my-best-header)
```

## Install ChocoLatey
1. Open PowerShell with admin rights
2. Input code:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```

## Install Google Cloud (GCloud) on Windows
1. Open PowerShell with admin rights
2. Input code:
    ```bash
    choco install -y gcloudsdk
    ```
3. Ensure you have latest version of GCloud
    ```bash
    gcloud components update
    ```

## Setup GCloud
Got from [this page](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#before_you_begin)
1. [Authenticate](#Authenticate-in-GCloud)
2. [Set current project](#Set-current-project-in-Google-Cloud)
3. [Set region](#Set-working-region-for-kubernetes-cluster)
4. [Set zone](#Set-working-zone-for-kubernetes-cluster)

## Authenticate in GCloud
1. Open PowerShell
2. Input code:
    ```bash
    gcloud auth login
    ```

## Get Google Cloud projects list
1. Open PowerShell
2. Input code:
    ```bash
    gcloud projects list
    ```

## Set current project in Google Cloud
1. Open PowerShell
2. Determine project id _(note: id, not name, it's important!)_
 to set (for example: `financials`)
    > See ["Get Google Cloud projects list"](#Get-Google-Cloud-projects-list)
3. Input code:
    ```bash
    gcloud config set project financials
    ```

## Get current project in Google Cloud
1. Open PowerShell
2. Input code:
    ```bash
    gcloud config get-value core/project
    ```

## Set working zone for kubernetes cluster
1. Open PowerShell
2. Choose zone on [this page](https://cloud.google.com/compute/docs/regions-zones/#available)
3. Input in console (for `us-central1-a`)
    ```bash
    gcloud config set compute/zone us-central1-a
    ```

## Set working region for kubernetes cluster
1. Open PowerShell
2. Choose region on [this page](https://cloud.google.com/compute/docs/regions-zones/#available)
3. Input in console (for `us-central1`)
    ```bash
    gcloud config set compute/region us-central1
    ```

## Get list of clusters from Google Cloud
1. Open PowerShell
2. Input in console
    ```bash
    gcloud container clusters list
    ```
### If you got exception
And exception looks like:
```text
ERROR: (gcloud.container.clusters.list) ResponseError: code=403, message=Required "container.clusters.list" permission(s) for "projects/financials". See https://cloud.google.com/kubernetes-engine/docs/troubleshooting#gke_service_account_deleted for more info.
```
First of all, check that you setup right project id
> See ["Set current project in Google Cloud"](#Set-current-project-in-Google-Cloud)

If it is right, follow the steps
1. Go to [APIs & Services page](https://console.cloud.google.com/projectselector2/apis)
2. Select your project
3. Click __Enable APIs and Services__
4. Search for Kubernetes, then select the API from the search results
5. Select __Kubernetes Engine API__
6. Click __Enable__
    1. If you have previously enable the API, click __Manage API__
    2. Click __Disable API__
    3. Then click __Enable__
7. Wait for a while, then try again get list of clusters

## Connect to Kubernetes cluster in Google Cloud
Got from [this post](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#generate_kubeconfig_entry)
1. Open PowerShell
2. Determine name of the cluster, you want connect to
   > See (Get list of clusters from Google Cloud)[#Get-list-of-clusters-from-Google-Cloud]

   > Note: Running gcloud container clusters get-credentials also changes the current context for kubectl to that cluster

## Deploy docker image to Google Cloud
Got from [this post](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

### Steps
1. Determine the hostname. Available options:
    * gcr.io (United States)
    * us.gcr.io (United States)
    * eu.gcr.io (European Union)
    * asia.gcr.io (Asia)

2. Determine the project id _(note: id, not name, it's important!)_
    > See ["Get Google Cloud projects list"](#Get-Google-Cloud-projects-list)    

3. Build or tag image with name, structured like this:
    ```text
    [HOSTNAME]/[PROJECT-ID]/[IMAGE]
    ```

4. Open PowerShell

5. Push image to the cloud (Docker client versions > 18.03):
    ```bash
    docker push us.gcr.io/financials-238018/client
    ```

6. _\[optional\]_ You can see result by link:
    ```text
    https://console.cloud.google.com/gcr/images/[PROJECT-ID]
    ```

## Install helm on Windows
1. Open PowerShell with admin rights
2. Input code:
    ```bash
    choco install -y kubernetes-helm
    ```

## Create helm chart
1. Open PowerShell
2. Go to directory, containing your project, for ex.:
    ```bash
    cd ~/PhpstormProjects/financials/client/
    ```
3. Go to one level up
    ```bash
    cd ../
    ```
4. Run `helm create` command with your project directory name
    ```bash
    helm create client
    ```
5. Go to your directory, now you can see 3 new files & 2 new directories:
    ```text
    client
    |-- Chart.yaml
    |-- charts
    |-- templates
    |   |-- tests
    |   |   `-- test-connection.yaml
    |   |-- NOTES.txt
    |   |-- _helpers.tpl
    |   |-- deployment.yaml
    |   |-- ingress.yaml
    |   `-- service.yaml
    `-- values.yaml
    ```
