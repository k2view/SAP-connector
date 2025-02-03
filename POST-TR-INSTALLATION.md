# Post TR Sanity Check

 ## Ensure the OData services are activated
 1. Open the tcode /n/iwfnd/maint_service
 2. Locate the Service using Search option
 ![](media/post-tr-installation/image-1.png)
 3. If the Service node is found, proceed to step 8. Else, click on Add Service button to register the service.
 4. Provide service name and click on Enter.
 5. Select the service and click on Add Selected Services.
 ![](media/post-tr-installation/image-2.png)
 6. Provide Package Assignment and click on Enter.
 ![](media/post-tr-installation/image-3.png)
 7. Make sure changes are saved to TR and Service is registered without any errors.
 ![](media/post-tr-installation/image-4.png)
 8. If the service is not activated, click on ICF Node dropdown and click on Activate. By default, the service would be activated when it is registered.
 ![](media/post-tr-installation/image-5.png)
 9. Make sure service is added and activated.
 ![](media/post-tr-installation/image-6.png)
 10. Repeat the steps [2 to 9] for all OData Services.

 ## Disable CSRF (Recommended)
 Unless specifically needed, it's recommended to disable CSRF token validation in order to avoid extra API calls to fetch/renew the CSRF token.
 1. Open the tcode sicf.
 2. Locate the Service either using Service Name and Execute (F8).
 3. Double click the Service Node.
![](media/post-tr-installation/image-7.png)
 4. Click on the change mode, to edit the Service and click on GUI Configuration.
 ![](media/post-tr-installation/image-8.png)
 5. Make sure the entry [~CHECK_CSRF_TOKEN, 0] is added to the Parameters.
 6. If the entry is not there, add the entry and save the changes.
 7. Repeat the steps [2 to 6] for all OData Services