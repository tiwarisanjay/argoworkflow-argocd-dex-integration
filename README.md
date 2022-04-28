# argocd-dex
ArgoCD with Dex Configuration
# ArgoCD Dex Integration with Microsoft connector 
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
        -  https://22.222.222.222       : If you port forward your application to localhost it should be `localhost:port`
                                            If you create a nodeport type service it should be `localhost:nodeport`
                                            If you create a loadbalancer it will be your `loadbalancerIP`
        - https://11.111.11.111:2746    : Its your argo workflow server `LoadBalancerIP:port`  or `localhost:port` 
-   Lets Install everything 
    ```
    kubectl apply -k microsoft-connector/
    ```
- If you are using localhost please port-forward you ArgoCD server and Argoworkflow server and update the config map with Right port. 
- Now port-forward your server to localhost or use the loadbalancer IP and you should see following screen: 
- URL should be : 
    -   https://localhost OR https://\<LoadBalancerIP>
    - Click on AZURETEST and it should authenticate your with AzureAD

    ![Login Page](ss/Login.JPG)