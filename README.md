# Generate Unique Emails by checking against Okta, O365, or G Suite

# Overview

To Onboard users in an organization, IT needs to generate unique Email for the end users. 

In this example uniqueness check is performed using different systems. You can pick the appropriate one for your organization.

1. Validate against O365 Proxy Addresses attribute for Organizations using O365
2. Validate against Google email aliases for Organizations using google mail
3. Validate the Proxy Address against Okta directly, if Okta is the master for provisioning users to the target mailSystem.
4. MultiDomain scenario

This flow follows the below logic.

1) Construct email with firstname.lastname@domain.com
2) Check in the email tenants for users starting with prefixes.
3) If already taken increment numbers at the end of lastname.

**Example**: John.doe@okta.com If there is a second John.Doe then the logic will create John.Doe1@okta.com

## Before you get Started / Prerequisites

Before you get started, you will need

1) Access to an Okta tenant with Okta Workflows enabled for your org
2) Access to Email tenants. Admin account required if you want to test against O365 or Google.

## Setup Steps

1.Import the Email workflow flopack in your workflow environment.
2. Inside the folder you should see 9 flows. Check for below main flows which triggers the emailGeneration based on your Domain and Mail System. 
*   Okta-New-Email-Generation
*   Google-Email-Generation
*   O365-Email-Generation
*   MultiDomain-Email-Generation
3. Depending on your email domain use one of the above flows. Only enable one of the above 4 flows.
4. Enable other subflows.
5. Establish the connections to the target apps inside the main workflow you choose.
6. Create a string array attribute in Okta called proxyAddresses.
7. Based on which ever flow you select from above, Open the email-Generation flow and in the end add the ProxyAddresses attribute to the update card and map it as per screenshot below

![image](https://user-images.githubusercontent.com/14205843/91586593-74fe7780-e90a-11ea-99c0-77e2c9449cac.png)

8. If you use “Google-Email-Generation” or “O365-Email-Generation” flow. Open the flow and update your email domain in the AssignEmailDomain card. If your O365 federated domain is jeykrish.com use @jeykrish.com
9.if you use “Okta-New-Email” generation follow the additional steps below.
*   Create a String-array attribute in Okta user profile called proxyAddresses	
*   Populate the ProxyAddresses attribute from your AD domain or create a sample value as below. This is to test the uniqueness against Okta.

    ![image](https://user-images.githubusercontent.com/14205843/92625624-bf261800-f27d-11ea-9fa7-21a99646c8e0.png)

10. If you have multiDomain in your organization, use the MultiDomain-Email-Generation. 

**To use the MultiDomain flow**

1. Setup an attribute in Okta called brandId. You can have any attribute. This attribute is used to determine the mailDomain for the user. For example you can also use company attributes. In this example you can setup a brandID attribute in Okta profile
2. In the Workflow open the “multiDomain-Email-Generation” flow. Click on the connections in the Read User card and save. Select the brandID attribute you created.
3. Map the brandID to the following card where we continue the flow only if it exists.  When you drag and drop select replace all when it pops up.
4. Click on the “Where Expression” section in the search table flow. Make sure the brandID is mapped there properly. Save the flow.
5. Now go to the table section and click on the provisioning table and update the table as below example.

![image](https://user-images.githubusercontent.com/14205843/90940864-24949080-e3c5-11ea-875e-5ba3f8415238.png)

6. In my example I have emailTenant representing different end points for validating Email uniqueness. If you just have one O365 instance with multiDomain you can remove this from the table and go back to the flow to change the logic. 

    **Multiple O365 tenants with multiple domains**

1. If you have the multiple O365 instances. Just go back to multidomain flow after completing step 6. Edit conditions in if else logic.

![image](https://user-images.githubusercontent.com/14205843/92623858-afa5cf80-f27b-11ea-9617-a7094f2b8db0.png)

2. For example you can use the brand domain instead of emailTenant. In that way based on the domain you can execute the logic.

                      ![image](https://user-images.githubusercontent.com/14205843/92624040-e7147c00-f27b-11ea-8953-31c224c557eb.png)

3. Inside the else section make sure you create cards exactly like the one in If. Since all are executing in O365. Follow the same steps if you use other email tenants.
4. Make sure all connections are setup to proper email tenants.

                 
         **Single O365 tenants with Multiple domains**

           
1. If you have a single O365 tenant with multiple domains. Use the “O365-Email-Generation” flow instead of MultiDomain flow.
2. Open the O365-Email-Generation flow and add the brandID and search Table cards as per multiDomain flow. 
3. Remove the Assign Domain section since there will be multiple domains.


## Testing this Flow

1. Create a user in Okta manually or using some import process. If you are creating manually and you want to use a MultiDomain scenario, make sure you set the brandID attribute as required in Okta profile. If you don't set this attribute as required, You won't be able to see this in the create user UI.
2. Once a user is created in Okta, that should trigger the workflow and generate a unique email and update Okta with Primary Email address and ProxyAddress in ProxyAddress attribute.

## Limitations & Known Issues

1. Okta workflows do not have any on-premise connector at this time of writing. So all the targets should be accessible in public and shall be exposed as an API endpoint.
