# uniqueEmail


### <span style="text-decoration:underline;">Overview</span>

To Onboard users in an organization, IT needs to generate unique Email for the end users. 

This flow generates unique Email address: 
Generate unique Email : In this example uniqueness check is performed using different systems. You can pick the appropriate one for your organization.
    a) Validate against O365 Proxy Addresses attribute for Organizations using O365
    b) Validate against Google email aliases for Organizations using google mail
    c) Validate the Proxy Address against Okta directly, if Okta is the master for provisioning users in the target mailSystem.
    
In this email example i use a attribute called BrandID which is used to determine the mail Domain 

example: Jeykrish.com,kriyahub.com etc. and target email system, Whether O365 or Okta or Google. If you just have a single email domain, You don't need to use this table and you can remove the brand logic inside the workflow.
    


### <span style="text-decoration:underline;">Before you get Started / Prerequisites</span>
Before you get started, you will need:

Access to an Okta tenant with Okta Workflows enabled for your org

You can trigger the flow in two ways.
   1) User create Event . Which is more Asyncronous way.  Okta workflows can generate a unique Email and update the Primary Email attribute.
   2) Import inline hook. Workflow is called during import process.Enabling import inline hook is documented here. 
           https://developer.okta.com/docs/reference/import-hook/#enabling-a-user-import-inline-hook
           
Attached flow pack is using the first method on create user event.

### <span style="text-decoration:underline;">Design consideration</span>

Should not use inlinehook if your flow is big. When Okta calls your external service, it enforces a default timeout of 3 seconds. Okta will attempt at most one retry. So if your flow is big and going to run for a longer time, you can use the Okta user create event to generate unique username.


### <span style="text-decoration:underline;">Setup Steps</span>

1.Import the Email workflow flopack in your workflow environment.
2.Create a table. Below is a sample data for the table. You can use your own domains for testing.

![image](https://user-images.githubusercontent.com/14205843/90940864-24949080-e3c5-11ea-875e-5ba3f8415238.png)

brandID represents attribute coming from HR system, emailTenent represents the target to check for email uniqueness. brandDomain represents the maildomain. Both emailTenant and brandDomain are derived from brandId. You can ignore this if you only one single domain.

3.Establish the connections to the target apps inside workflow, O365,GoogleApps and Okta.
4. Update the connection in the flows.
5. Extend the Okta profile with proxyAddresses attribute(String Array).You can also import the existing ProxyAddresses from your AD and have Okta update for new users.

### <span style="text-decoration:underline;">Testing this Flow</span>

1) Create a user in Okta manually or using some import process. 
2) Once user is created in Okta. That should trigger the workflow and generate unique email and update Okta with Primary Email address and ProxyAddress in ProxyAddress attribute.


### <span style="text-decoration:underline;">Limitations & Known Issues</span>
1) Okta workflows does not have any on-premise connector at this time for writing. So all the target should be accessible in public and shall be exposed as an API endpoint.
