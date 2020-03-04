# ** Build a website that requires user to authenticate with AWS ELB/Cognito **

## **Overview**
In this lab we, you will be building a website where users need to be authenticate before accessing the content using Amazon Application Load Balancer (ALB)  and integrating with Amazon Cognito. AWS ALB provides authentication through social Identity Providers (IdP) which will be Amazon  Cognito. When a user browse to your website, ALB redirects them to Cognito which will handle the Login and redirect them back to your ALB. 


## **Architecture**
![Architecture](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/aws-security-week-cloudscale-authentication-advanced-authorization-with-amazon-cognito-amazon-cloud-directory-18-638.jpg)

## **Walkthrough**
1. Configure Cognito User pool
2. Congigure Authentication rule on ALB

### **Configure Cognito User pool**

Web developers want to use federated identities from social networks to allow their users to sign-in. ALB’s new authentication action provides authentication through social Identity Providers (IdP) like Google, Facebook, and Amazon through Amazon Cognito.
We will use Cognito to act as an identity provider (IdP) which will authenticate users through the user pools supported by Amazon Cognito. 

1. Open the Amazon Cognito Console in Asia Pacific(Sydney) Region at https://ap-southeast-2.console.aws.amazon.com/cognito/
2. Click on Manage User Pools and select the User Pool created by the CloudFormation Template "ELBDevlabs-user-pool". A user pool is a user directory in Amazon Cognito and we will refer to this when we will configure the Authentication on the Load Balancer.

![CognitoUserPool](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.12.11+PM.png)

3. Create user in the Users and groups navigation pane and fill out the user form (you can choose either phone or email). This where all the Users related information will be stored

![UserCreation](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.17.25+PM.png)

4. After you create a user pool, you can create an app to use the built-in webpages for signing up and signing in your users.

Configure the App client settings from the App integration navigation pane :


![AppConf](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.34.41+PM.png)

  a. Select "Cognito User Pool"
  
  b. Configure the callback URL(s) : https://elbdevlabs.yecine.myinstance.com/oauth2/idpresponse
  This tells Cognito where to redirect the User once authenticated, Cognito needs to redirect the authenticated user back to the LoadBalancer. To find out what is the DNS name of your LoadBalancer, please refer to the 
  
  c. Allowed  OAuth Flows: Authorization code grant
  
  d. Allowed OAuth Scopes : openid
  
  e. Save changes

5. Configure the Domain name for your Cogntio endpoint and check its availability with the "Check availability" button, then hit "Save changes"

![Domain](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.39.18+PM.png)


### **Configure ALB Authentication rule**

1. Open the Amazon EC2 Console in Asia Pacific (Sydney) Region at https://ap-southeast-2.console.aws.amazon.com/ec2/
2. Select the Load Balancers menu from the navigation pane
3. Select the Load Balancer that was created as part of the CloudFormation template 
4. At the bottom, select the Listener Tab and select the "View/Edit rules" 

![ELBAuthRule](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-03-05+at+10.04.28+AM.png)


5. Edit the default rule and delete the default action. Replace "Forward" by with "Authenticate"

![DefRule](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.46.04+PM.png)

6. Select Amazon Cognito and choose the Pool Id that we configured previously 
7. Select App Client we configured (there should only one App Client)
8. Leave by default the other parameters and click on the Validation Box

![CognitoPool](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.47.11+PM.png)

9. After the first Authenticate rule, add a new action "Forward to" and select the Target group that was created as part of CloudFormation 

![Vali](https://customsolutions.s3-ap-southeast-2.amazonaws.com/Yecine-Devlab/Screen+Shot+2020-02-24+at+3.48.02+PM.png)

10. Validate the settings and click on the Update button


### **Try it***
Browse to your ALB Domain name configured for the website : https://elbdevlabs.yecine.myinstance.com
