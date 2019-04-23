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

## Google Cloud (GCloud)
### Install Google Cloud on Windows
1. Open PowerShell with admin rights
2. Input code:
    ```powershell
    choco install -y gcloudsdk
    ```
3. Ensure you have latest version of GCloud
    ```powershell
    gcloud components update
    ```

### Setup GCloud
Got from [this page](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#before_you_begin)
1. [Authenticate](#Authenticate-in-GCloud)
2. [Set current project](#Set-current-project-in-Google-Cloud)
3. [Set region](#Set-working-region-for-kubernetes-cluster)
4. [Set zone](#Set-working-zone-for-kubernetes-cluster)

### Authenticate in GCloud
1. Open PowerShell
2. Input code:
    ```powershell
    gcloud auth login
    ```

### Get Google Cloud projects list
1. Open PowerShell
2. Input code:
    ```powershell
    gcloud projects list
    ```

### Set current project in Google Cloud
1. Open PowerShell
2. Determine project id _(note: id, not name, it's important!)_
 to set (for example: `financials`)
    > See ["Get Google Cloud projects list"](#Get-Google-Cloud-projects-list)
3. Input code:
    ```powershell
    gcloud config set project financials
    ```

### Get current project in Google Cloud
1. Open PowerShell
2. Input code:
    ```powershell
    gcloud config get-value core/project
    ```

### Set working zone for kubernetes cluster
1. Open PowerShell
2. Choose zone on [this page](https://cloud.google.com/compute/docs/regions-zones/#available)
3. Input in console (for `us-central1-a`)
    ```powershell
    gcloud config set compute/zone us-central1-a
    ```

### Set working region for kubernetes cluster
1. Open PowerShell
2. Choose region on [this page](https://cloud.google.com/compute/docs/regions-zones/#available)
3. Input in console (for `us-central1`)
    ```powershell
    gcloud config set compute/region us-central1
    ```

### Get list of clusters from Google Cloud
1. Open PowerShell
2. Input in console
    ```powershell
    gcloud container clusters list
    ```
#### If you got exception
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

### Connect to Kubernetes cluster in Google Cloud
Got from [this post](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#generate_kubeconfig_entry)
1. Open PowerShell
2. Determine name of the cluster, you want connect to
   > See (Get list of clusters from Google Cloud)[#Get-list-of-clusters-from-Google-Cloud]

   > Note: Running gcloud container clusters get-credentials also changes the current context for kubectl to that cluster

### Deploy docker image to Google Cloud
Got from [this post](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

#### Steps
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
    ```powershell
    docker push us.gcr.io/financials-238018/client
    ```

6. _\[optional\]_ You can see result by link:
    ```text
    https://console.cloud.google.com/gcr/images/[PROJECT-ID]
    ```

### Reserve global static IP address for Ingress in GCloud
Got from [here](https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip#step_2b_using_an_ingress)
1. Open PowerShell
2. Reserve global static IP address for your application (for example, will name it `financials-ip`)
    > Ingress works only with global static IP addresses, not regional
    ```powershell
    gcloud compute addresses create financials-ip --global
    ```
3. Let's see what IP was created for you by GCloud
    ```powershell
    gcloud compute addresses describe financials-ip --global
    ```

## Helm
### Install helm on Windows
1. Open PowerShell with admin rights
2. Input code:
    ```powershell
    choco install -y kubernetes-helm
    ```

### Create helm chart
1. Open PowerShell
2. Go to directory, containing your project, for ex.:
    ```powershell
    cd ~/PhpstormProjects/financials/client/
    ```
3. Go to one level up
    ```powershell
    cd ../
    ```
4. Run `helm create` command with your project directory name
    ```powershell
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
### Try helm config
1. Open PowerShell
2. Move to directory with Chart.yml
    ```powershell
    cd ~/PhpstormProjects/financials/client/
    ```
3. Input code:
    ```powershell
    helm install --dry-run --debug .
    ```

#### Errors
```text
Error: grpc: received message larger than max
```
Just fill your `.helmignore` file.
It should ignore all source code (and deps, for example: node_modules)

### Deploy helm config
1. Open PowerShell
2. Move to directory with Chart.yml
    ```powershell
    cd ~/PhpstormProjects/financials/client/
    ```
3. Input code (deploy service `client`):
    ```powershell
    helm install --name client .
    ```

### Upgrade helm config
1. Open PowerShell
2. Move to directory with Chart.yml
    ```powershell
    cd ~/PhpstormProjects/financials/client/
    ```
3. Input code (deploy service `client`):
    ```powershell
    helm upgrade client . --recreate-pods
    ```

### External access to container
By default, the chart will create a ClusterIP type Service, 
so NGINX will only be exposed internally in the cluster. 
To access it externally, you should use the NodePort type instead.

### Install Ingress with helm
1. Open PowerShell
2. Input code:
    ```powershell
    helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true
    ```

## Kubernetes
### Get info about pods
1. Open PowerShell
2. Input code:
    ```powershell
    kubectl get pods
    ```
### Get info about services
1. Open PowerShell
2. Input code:
    ```powershell
    kubectl get services
    ```
    or
    ```powershell
    kubectl get svc
    ```
### Get info about service
1. Open PowerShell
2. Input code:
    ```powershell
    kubectl get service nginx-ingress-controller
    ```
### Get info about nodes
Got from [here](https://cloud.google.com/kubernetes-engine/docs/how-to/exposing-apps#creating_a_service_of_type_nodeport)
1. Open PowerShell
2. Input code:
    ```powershell
    kubectl get nodes --output wide
    ```

### Proxy service to local
Got from [here](https://cloud.google.com/solutions/continuous-integration-helm-concourse#deploying_a_change_to_the_application)
1. Open PowerShell
2. Determine pod name, that you want to proxy
    > See [Get info about pods](#Get-info-about-pods)
3. Input code (proxy pod `client-5679767f4d-hwns9`, local ip `8080`, pod port `80`)
    ```powershell
    kubectl port-forward client-5679767f4d-hwns9 8080:80
    ```

### Expose NodePort
1. Open PowerShell
2. Determine PORT, that you wish to expose
    > See [Get info about pods](#Get-info-about-service)
    
    > You should use second value of port, for example in
    ```text
    client    NodePort   10.55.248.219   <none>        80:31620/TCP   1h
    ```
    > you should choose port 31620
2. Input code (expose port `31620`):
    ```powershell
    gcloud compute firewall-rules create test-node-port --allow tcp:31620
    ```
3. See your [node IPs](#Get-info-about-nodes)
4. Go to $(your node ip):$(port) to see your application

### Expose with Ingress
Got from [this page](https://cloud.google.com/community/tutorials/nginx-ingress-gke#nginx_ingress_controller_on_kubernetes_engine)
1. [Install helm](#Install-helm-on-Windows)
2. Install tiller
    > TODO: Add steps
3. Set values for sections:
    > Work when you created config with helm create and templates/ingress component
    ```yaml
    ingress:
      enabled: true
      annotations: {}
        # kubernetes.io/ingress.class: nginx
        # kubernetes.io/tls-acme: "true"
      hosts:
        # Host must be commented, until you have
        # - host: chart-example.local
        - paths:
            - /
    ```
4. [Deploy your helm config](#Deploy-helm-config)
5. [Install Ingress with helm](#Install-Ingress-with-helm)
6. Check, that ingress see your service
    ```powershell
    kubectl get ingress
    ```
7. Look your IP in [Get info about services](#Get-info-about-services)

### Setup static IP for Ingress with GCloud
Got from [here](https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip#step_2b_using_an_ingress)
1. [Reserve global static IP address](#Reserve-global-static-IP-address-for-Ingress-in-GCloud)
2. To expose a web application on a 
static IP using Ingress, you need to deploy two resources:
    * [A Service with type:NodePort](#External-access-to-container)
    * An Ingress configured with the service name and static IP annotation
        * Annotation should looks like
        ```yaml
        kubernetes.io/ingress.global-static-ip-name: financials-ip
        ```
         where `financials-ip` - the name of global static IP address
3. _\[optional\]_ To see the reserve IP address associated with the load balancer:
    ```powershell
    kubectl get ingress
    ```
