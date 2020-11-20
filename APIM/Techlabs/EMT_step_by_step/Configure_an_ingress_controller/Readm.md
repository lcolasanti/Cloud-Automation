# Configure an Ingress Controller

What we are going to do
- 

TODO -> SCHEMA Here

RG_NAME_NETWORK=
PUBLIC_IP_NAME=
RG_NAME_DNS
YOUR_DNS_ALIAS
YOUR_PUBLIC_IP

- Create a public IP into AKS Ressource Group
    First, we need to cerate a public address to join AKS

    Execute the following command to create a public IP [documentation](https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest#az_network_public_ip_create)
    ``` Bash
    az network public-ip create --resource-group <<RG_NAME_NETWORK>> --sku Standard --name <<PUBLIC_IP_NAME>> --allocation-method static --query publicIp.ipAddress -o tsv --allocation-method static
    ```

    Output command
    ``` Bash
    YOUR PUBLIC IP ADDRESS
    ```

- Create DNS zone
Then we need DNS records in order to be able to connect to AKS and to APIM UIs (manager, manageent)
    - Record creation of A type [information](https://pressable.com/2019/10/11/what-are-dns-records-types-explained-2/)
        ``` Bash

        az aks show --resource-group RG-apim-demo7 --name apim2-demo7-aks --query nodeResourceGroup -o tsv

        az network dns record-set a add-record -g <<RG_NAME_DNS>> -z azure.demoaxway.com -n <<YOUR_DNS_ALIAS>> -a <<YOUR_PUBLIC_IP>>
        ```
        Output command
        ``` Bash
        {
          "arecords": [
            {
              "ipv4Address": "<<YOUR_PUBLIC_IP>>"
            }
          ],
          "etag": "7101c679-aca0-494c-975b-5cfb87bfb886",
          "fqdn": "ddi.azure.demoaxway.com.",
          "id": "/subscriptions/f0202431-2a18-4bea-8ae0-f3b4c723bbdb/resourceGroups/RG-DEMO-MARKETPLACE-AZURE/providers/Microsoft.Network/dnszones/azure.demoaxway.com/A/ddi",
          "metadata": null,
          "name": "ddi",
          "provisioningState": "Succeeded",
          "resourceGroup": "RG-DEMO-MARKETPLACE-AZURE",
          "targetResource": {
            "id": null
          },
          "ttl": 3600,
          "type": "Microsoft.Network/dnszones/A"
        }
        ```

    - Record creation of CNAME type [information](https://pressable.com/2019/10/11/what-are-dns-records-types-explained-2/)

        - DNS record to join ANM web UI
        ``` Bash
        az network dns record-set cname set-record -g <<RG_NAME_DNS>> -z azure.demoaxway.com -n anm.<<YOUR_DNS_ALIAS>> -c <<YOUR_DNS_ALIAS>>.azure.demoaxway.com
        ```
        
        Output command
        ``` Bash
        {
          "cnameRecord": {
            "cname": "ddi.azure.demoaxway.com"
          },
          "etag": "7d7b3e19-20ae-4dd8-bacd-ad85683f516b",
          "fqdn": "anm.ddi.azure.demoaxway.com.",
          "id": "/subscriptions/f0202431-2a18-4bea-8ae0-f3b4c723bbdb/resourceGroups/RG-DEMO-MARKETPLACE-AZURE/providers/Microsoft.Network/dnszones/azure.demoaxway.com/CNAME/anm.ddi",
          "metadata": null,
          "name": "anm.ddi",
          "provisioningState": "Succeeded",
          "resourceGroup": "RG-DEMO-MARKETPLACE-AZURE",
          "targetResource": {
            "id": null
          },
          "ttl": 3600,
          "type": "Microsoft.Network/dnszones/CNAME"
        }
        ```

        - DNS record to join API web UI
        ``` Bash
        az network dns record-set cname set-record -g <<RG_NAME_DNS>> -z azure.demoaxway.com -n api.<<YOUR_DNS_ALIAS>> -c <<YOUR_DNS_ALIAS>>.azure.demoaxway.com
        ```

        Output command
        ``` Bash
        {
          "cnameRecord": {
            "cname": "ddi.azure.demoaxway.com"
          },
          "etag": "e5f956da-0a1d-4bf9-88b4-be223be92b02",
          "fqdn": "api.ddi.azure.demoaxway.com.",
          "id": "/subscriptions/f0202431-2a18-4bea-8ae0-f3b4c723bbdb/resourceGroups/RG-DEMO-MARKETPLACE-AZURE/providers/Microsoft.Network/dnszones/azure.demoaxway.com/CNAME/api.ddi",
          "metadata": null,
          "name": "api.ddi",
          "provisioningState": "Succeeded",
          "resourceGroup": "RG-DEMO-MARKETPLACE-AZURE",
          "targetResource": {
            "id": null
          },
          "ttl": 3600,
          "type": "Microsoft.Network/dnszones/CNAME"
        }
        ```

        - DNS record to join API Manager web UI
        ``` Bash
        az network dns record-set cname set-record -g <<RG_NAME_DNS>> -z azure.demoaxway.com -n api-manager.<<YOUR_DNS_ALIAS>> -c <<YOUR_DNS_ALIAS>>.azure.demoaxway.com
        ```

        Output command
        ``` Bash
        {
          "cnameRecord": {
            "cname": "ddi.azure.demoaxway.com"
          },
          "etag": "651c0b4c-2da0-458b-b4bd-b08628efd4be",
          "fqdn": "api-manager.ddi.azure.demoaxway.com.",
          "id": "/subscriptions/f0202431-2a18-4bea-8ae0-f3b4c723bbdb/resourceGroups/RG-DEMO-MARKETPLACE-AZURE/providers/Microsoft.Network/dnszones/azure.demoaxway.com/CNAME/api-manager.ddi",
          "metadata": null,
          "name": "api-manager.ddi",
          "provisioningState": "Succeeded",
          "resourceGroup": "RG-DEMO-MARKETPLACE-AZURE",
          "targetResource": {
            "id": null
          },
          "ttl": 3600,
          "type": "Microsoft.Network/dnszones/CNAME"
        }
        ```

- Install NNGINX
Now we need to install an ingress controller. 
To do so, we are using NGINX offcial HELM who will deploy NGINX as Ingress Controller into our AKS

    - Adding NGINX repository into HELM
    First we need to add NGINX repository into HELM repo :
    ``` Bash
    helm repo add nginx-stable https://helm.nginx.com/stable
    helm repo update
    ```

    Output command
    ``` Bash
    "nginx-stable" has been added to your repositories
    
    Hang tight while we grab the latest from your chart repositories...
    ...Successfully got an update from the "nginx-stable" chart repository
    Update Complete. ⎈Happy Helming!⎈
    ```

    - Deploying NGINX
    Then we can deploy by launching an HELM install :
    ``` Bash
    helm install nginx-ingress nginx-stable/nginx-ingress --namespace <<K8S_NAMESPACE_NAME>> --set controller.replicaCount=2,controller.nodeSelector."beta\.kubernetes\.io/os"=linux,defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux,controller.service.externalTrafficPolicy=Local,controller.service.loadBalancerIP="<<YOUR_PUBLIC_IP>>",rbac.create=true --set-string controller.config.use-http2=false,controller.ssl_procotols=TLSv1.2        
    ```

    Output command
    ``` Bash
    NAME: nginx-ingress
    LAST DEPLOYED: Mon Nov  9 16:32:45 2020
    NAMESPACE: k8s-ddi-apim-emt
    STATUS: deployed
    REVISION: 1
    TEST SUITE: None
    NOTES:
    The NGINX Ingress Controller has been installed.
    ```