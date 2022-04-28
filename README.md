# argocd-dex
ArgoCD with Dex Configuration
# ArgoWorkflow/ArgoCD Dex Integration AzureAD using dex Microsoft connector 
## Prerequsites 
- Create new application in AzureAD OIDC follow following link for the same
    [Quick Start : Register an application](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app)
- Call back URL in your AzureAD application would be : 
    - https://<ArgoCD_Server_IP/URL>/api/dex/callback
- I worked with GKE so still needs to work with localhost. It should work for localhost too where the IP/URL is required.
 
## Lets run some commands now to get it work
### Microsoft Connector

- Following file needs to be updated: 
    - microsoft-connector/argocd-extra.yaml , microsoft-connector/argoworkflow-extra.yaml 

        -  \<Your Base64 Client Secret>  : with your ClientSecret created in AzureAD for OIDC application
        -  <Your Clinet/ApplicationID of Azure app> : Client/Application ID in AuzreAD OIDC Application. 
        - 111111-000-000-aaaa-aaaaaaaa : Update Tenant-ID with yours. 
        -  https://22.222.222.222       : If you port forward your application to localhost it should be `localhost:port`
                                            If you create a nodeport type service it should be `localhost:nodeport`
                                            If you create a loadbalancer it will be your `loadbalancerIP`
        - https://11.111.11.111:2746    : Its your argo workflow server `LoadBalancerIP:port`  or `localhost:port` 

-   Lets Install everything 
    ```
    kubectl apply -k microsoft-connector/
    ```
- Restart your argo-server, argocd-server and dex pod if you are seening error as they might not have picked the changes from configmap. 
- If you are using localhost please port-forward you ArgoCD server and Argoworkflow server and update the config map with Right port. 
- Now port-forward your argo workflow server to localhost or use the loadbalancer IP. 
- Click on Single Sign On. 
- Congratulations you should have authenticated with your AzureAD account. :) 

## Failures Points for me: 
1.  If you mention wrong URL for Issuer in `argocd-cm` configmap it will keep on giving you `404 page not found` error. 
2. If you mention wrong issuer url for `workflow-controller-configmap` configmap it will also give you `404 page not found` error. 
3. If you have not use `insecureSkipVerify: true` flag in `workflow-controller-configmap` argo-workflow server pod will give you certificate error. 
4. If your ID is incorrect for `staticClient` setup in `argocd-cm` configmap it wil give you `invalid client_id("your-right-id")` error. Though you will see the Right ClientID in your msg and this is pretty confusing and took me 3 days to solve the issue as nothing was pointing where its wrong. 