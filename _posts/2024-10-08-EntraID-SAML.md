## How to Create a New Entra ID Enterprise Application and Configure Custom Attributes for SAML Login for AWS Cognito

When integrating Entra ID (formerly Azure AD) with AWS Cognito for SAML login, it's important to use a unique attribute to identify users. In this guide, we'll walk you through the steps to create a new Enterprise Application in Entra ID and configure a custom attribute named "user.objectid". This attribute ensures that user identities remain consistent even if other attributes, such as last names, change.

## Why Use "user.objectid"?

The "user.objectid" attribute is unique to each user in Entra ID and does not change, even if other user attributes are updated. This is particularly important for scenarios where an employee changes their last name, as other attributes will be updated with the new value. Using "user.objectid" prevents the creation of a new local user in your application and ensures that existing user data is preserved.

## Steps to Create a New Enterprise Application in Entra ID

### 1. Create a New Application

* <strong>Navigate to the Azure Portal</strong>
    - Open the Azure Portal and go to the Entra ID service.

* <strong>Create a New Application</strong>
    - Go to <strong>Enterprise applications</strong> and select <strong>New application</strong>.
    - Choose <strong>Create your own application</strong>.
    - Select the option to <strong>Integrate any other application you don’t find in the gallery (Non-gallery)</strong>.
    - Type the name of your new application and create it.

<a href="/images/entra_cognito/create_ent_app.png" target="_blank">
    <img src="/images/entra_cognito/create_ent_app.png" alt="Create Enterprise Application" style="width: 80%; display: block; margin: 0 auto;">
</a>

### 2. Configure Single Sign-On (SSO) Login

Create the connection between Entra ID and your application by setting the login URL and the identity of your application.

1. <strong>Select Single Sign-On Method</strong>
    - In your Enterprise application, select the <strong>Single Sign-On</strong> option from the left menu.
    - Click on <strong>SAML</strong> as the single sign-on method.

2. <strong>Edit Basic SAML Configuration</strong>
    - Edit the <strong>Basic SAML Configuration</strong>.
    - Add the <strong>Identifier (Entity ID)</strong> and <strong>Reply URL (Assertion Consumer Service URL)</strong>.
        - The <strong>Identifier (Entity ID)</strong> should follow the format: `urn:amazon:cognito:sp:<Cognito_userpool_ID>`.
        - The <strong>Reply URL (Assertion Consumer Service URL)</strong> should follow the format: `https://<Cognito_domain_URL>/saml2/idpresponse`.

3. <strong>Save the Configuration</strong>
    - Save the changes to the Basic SAML Configuration.

<a href="/images/entra_cognito/saml_sso_config.png" target="_blank">
    <img src="/images/entra_cognito/saml_sso_config.png" alt="SAML SSO Configuration" style="width: 80%; display: block; margin: 0 auto;">
</a>

### 3. Configure User Access for SSO Login

Assign the users and groups that should have permissions to log in to your application.

1. <strong>Assign Users and Groups</strong>
    - Select the <strong>Users and groups</strong> option from the Enterprise application options.
    - Click on <strong>Add user/group</strong>.
    - Select the users and/or groups who should have access to your application.
    - Confirm your selections and save.

<a href="/images/entra_cognito/sso_add_users.png" target="_blank">
    <img src="/images/entra_cognito/sso_add_users.png" alt="Add Users for SSO" style="width: 80%; display: block; margin: 0 auto;">
</a>

### 4. Configure User Attributes & Claims for SSO Login

Configure which Entra ID attributes should be used to log in to your application.

1. <strong>Edit User Attributes & Claims</strong>
    - From the <strong>Single Sign-On</strong> option for your Enterprise application, edit the <strong>User Attributes & Claims</strong>.

<a href="/images/entra_cognito/sso_attributes_claims.png" target="_blank">
    <img src="/images/entra_cognito/sso_attributes_claims.png" alt="User Attributes and Claims" style="width: 80%; display: block; margin: 0 auto;">
</a>

2. <strong>Set Unique User Identifier</strong>
    - Select the <strong>Unique User Identifier (Name ID)</strong> claim to edit it.
    - In the <strong>Source attribute</strong>, set the value to `user.objectid`.

<a href="/images/entra_cognito/sso_object_id_claim.png" target="_blank">
    <img src="/images/entra_cognito/sso_object_id_claim.png" alt="Set Object ID Claim" style="width: 80%; display: block; margin: 0 auto;">
</a>

3. <strong>Save the Changes</strong>
    - Save the changes.

## Update the AWS Cognito User Pool

Once you have defined all the claim mappings on the Entra ID side, it is time to connect the dots on AWS's side.

### 1. Retrieve SAML Federation Metadata

This is the intermediate step between configuring Entra ID and Cognito.

1. <strong>Get the Federation Metadata URL</strong>
    - In your Entra ID Enterprise application, navigate to the <strong>Single Sign-On</strong> section.
    - Locate the <strong>App Federation Metadata Url</strong>.
    - Copy this URL, as it will be needed in AWS Cognito.

<a href="/images/entra_cognito/sso_metadata_url.png" target="_blank">
    <img src="/images/entra_cognito/sso_metadata_url.png" alt="Federation Metadata URL" style="width: 80%; display: block; margin: 0 auto;">
</a>

### 2. Enable the IdP

After all configurations are done on the Entra ID side, you need to update the configuration in Cognito.

1. <strong>Enable the IdP</strong>
    - In AWS Cognito, select the User Pool and go to the <strong>Sign-in experience</strong> tab.
    - Under the <strong>Federated identity provider sign-in</strong> section, click on <strong>Add identity provider</strong>.
    - Select the <strong>SAML</strong> type.
    - Enter a name under <strong>Provider name</strong>.
    - Under <strong>Identifiers</strong>, add the <strong>IdpIdentifier</strong> value.
    - Use the recommended setting <strong>Require SP-initiated SAML assertions</strong> for the <strong>IdP-initiated SAML sign-in</strong> setting.
    - Enter the metadata document endpoint URL saved previously.

<a href="/images/entra_cognito/sso_cognito_saml_config.png" target="_blank">
    <img src="/images/entra_cognito/sso_cognito_saml_config.png" alt="Cognito SAML Configuration" style="width: 80%; display: block; margin: 0 auto;">
</a>

This is a better solution than uploading the XML file because Cognito refreshes the metadata every 6 hours or before the metadata expires. This way, you don’t have to manually refresh the metadata XML every time the Entra ID SSL certificates expire or any other change occurs on the Entra ID side that would impact the federation authentication.

### 3. Configure Attribute Mapping

Configure the attributes that are stored in Entra ID and are mapped via the SAML schema in AWS Cognito. Here is a copy-and-paste friendly table for easier usage:

| SAML Attribute | User Pool Attribute | 
|----------------|---------------------|
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn` | Profile |
| `http://schemas.xmlsoap.org/claims/CommonName` | Preferred User Name |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` | Given Name |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` | Family Name |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` | Email |

<a href="/images/entra_cognito/sso_cognito_attributes.png" target="_blank">
    <img src="/images/entra_cognito/sso_cognito_attributes.png" alt="Cognito Attribute Mapping" style="width: 80%; display: block; margin: 0 auto;">
</a>

### 4. Enable the External IdP for App Clients

Now that you have an IdP using the Entra ID configuration, you need to assign it to your application created in the Cognito user pool.

1. <strong>Enable the IdP for App Clients</strong>
    - In AWS Cognito, navigate to the <strong>App integration</strong> tab, <strong>App client list</strong> section.
    - Select the App client you want to configure and edit the <strong>Hosted UI</strong> section.
    - From the <strong>Identity providers</strong> dropdown, select your newly created IdP (e.g., `EntraID`) and save the changes.

### 5. Test the Configuration

1. <strong>Test the Hosted UI</strong>
    - Click on the <strong>View Hosted UI</strong> button to quickly test your changes.

### 6. Final Steps

After a user has successfully authenticated via the external IdP, it will automatically be created in your User Pool with the "Enabled" status. The confirmation status will be set to `EXTERNAL_PROVIDER`.

The user attribute `identities` will store the metadata relating to the external IdP that “owns” this identity. This includes the user’s ID in the external IdP’s attribute, in our case, the "Identifier (Entity ID)".

<a href="/images/entra_cognito/sso_cognito_identities.png" target="_blank">
    <img src="/images/entra_cognito/sso_cognito_identities.png" alt="Cognito Identities" style="width: 80%; display: block; margin: 0 auto;">
</a>

These fields will be updated on each successful authentication, so you can rely on the fact that the fields you receive via JWT attributes will be up-to-date.

### Conclusion

By following these steps, you will have a fully functioning solution offering federated authentication with an external Entra ID. This setup ensures that user identities remain consistent and up-to-date, even when user attributes change.

---

Feel free to add more details or modify the content as needed. Let me know if you need any further assistance or additional information for the next steps!
