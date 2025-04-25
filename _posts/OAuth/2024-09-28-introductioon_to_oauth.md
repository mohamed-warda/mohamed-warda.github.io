---
title: OAuth2.1 Part 1 - The Problem, Solution, Terminology, Flows

date: 2024-09-28 8:00:00 +/-0200
categories: [OAuth]
tags: [OAuth2.1, Security, Authorization] # TAG names should always be lowercase
---
First, Let's Discuss the Problem ..!(done)
Second, Let's Discuss the Solution ..!
Introdcution and the  history of oauth
Flows
Terminology You Need to Know(done)

![Image](/assets/img/posts/intro_oauth/what_is_oauth.jpg)

```cs
public class HelloWorld
{
    public static void Main()
    {
        Console.WriteLine("Hello, world!");
    }
}
```





<h3 id='the-problem' style="font-weight: bold;">First, Let's Discuss the Problem ..!</h3>

#### Does this image look familiar? (Facebook)  
![Image](/assets/img/posts/intro_oauth/facebook.webp)

If you were around during 2008–2010, when Facebook was rapidly growing, you may have seen this before. At that time, Facebook asked users to enter their Gmail address and password so it could access their contact list and suggest friends who were already on the platform. Do you see the problem with this method?

#### A Clearer Example (Yelp)  
![Image](/assets/img/posts/intro_oauth/yelp.webp)

This is an image of Yelp from around 2008–2011. During sign-up, it would ask you to enter your Gmail address along with your actual password. Yelp used your credentials to log in to your Gmail account on your behalf in order to access your contact list. This allowed Yelp to see which of your friends were already using Yelp and which were not, so you could invite them. Since Yelp was just starting out and had not yet built a strong reputation, many users were hesitant to trust it with sensitive information.

Do you see the problem now? Providing your credentials to another app can be risky. Many people used the same Gmail credentials across various platforms, including banking services, which posed a significant security risk. Sharing login credentials with an unverified site was not only unsafe but also a poor security practice. Although Yelp promised that it would only access your contact list, it still had your password. Moreover, this approach created complications when connecting to third-party apps. For example, if you were connected to both Facebook and Yelp and later decided to disconnect from Yelp, you would have to change your Gmail password thereby also unintentionally disconnect from Facebook.

_**So here is the big question:**_  
**How can I let a website access my data without giving it my password?**

---------------------

<h3 id='the-solution' style="font-weight: bold;">Second, Let's Discuss the Solution ..!</h3>

#### So how can solve this ..?
From a high-level perspective, first we can start by not giving third party apps our password directly. Maybe we can give them something else, like an access token, that they can use to obtain the resource they need,like the Gmail Contacts List, for example. That token would only be allowed, or have the privilege, to access the Contacts List and only that, sO yelp wouldn’t be able to do more than that. For example, it wouldn’t be able to send emails on your behalf.

So the flow would be like this: instead of Yelp asking me for my Gmail address and password, it would redirect me to the Gmail login page itself. Then I enter my email and password, and if I log in successfully, Gmail would somehow redirect me back to Yelp with the access token in the query parameters, where Yelp can extract it and use it to call the gmail APIs to get the user's contact list, **Seems better, right ?**

#### So Now Come the Other Big Question, What is OAuth ..?



<h3 id='the-terminology' style="font-weight: bold;">First Some Terminology You Need to Know</h3>



- **Resource** → Something you own as a user.  
- **Resource Owner** → You as a user.  
- **Resource Server** → The server that hosts the resource.  
- **User Agent** → The application used to interact with the client, such as a browser or mobile app.
- **Client** → Your web app. From the OAuth server's perspective, it is a client because it requests access tokens from the authorization server.

- **Scope** → defines the specific data and actions the client is permitted to access. For example, a scope might grant read-only access to contacts or full access to a user’s profile. The **Authorization Server** maintains a predefined list of supported scopes, and the client must request scopes that align with the server’s allowed permissions, is more simple word this is what we send to tell the auth server what we want to have permission to

- **Consent/Consent Screen** →  The explicit permission granted by the user to the authorization server, allowing the client to access their data based on the requested scope. For instance, when logging into a website via Google, a pop-up appears after authentication asking, “Are you sure you want to give ‘**Client_Name**’ access to your profile?” This is the consent screen.

- **Redirect URL** → The URL where the user is redirected after authorization (also known as a **callback URL**).
- **Authorization Grant** → The result of the authorization flow. This grant is used to obtain an access token. In the authorization code flow, the grant is the temporary **code** returned to the client.
 
- **Access Token** → A token issued by the authorization server to the client. The client uses this token to access the resource server (external service).
  - _**Note:**_ he access token is opaque to the client. The client simply sends it to the resource server, which is responsible for validating and interpreting the token.

- **Authorization Server** → The server responsible for generating tokens. It must implement two endpoints (e.g., token and authorization endpoints).
  - _**Note:**_ The authorization server and resource server may be the same server.
	
- **Back Channel (Highly Secure)** → A secure server-to-server communication channel (HTTPS) that is not exposed to user interference or interception. This does not exclusively mean backend-to-backend communication. For example, a Single-Page Application (JavaScript client) can use the back channel by making direct HTTPS calls without relying on browser redirects making HTTPS API calls **directly** from its code to the authorization server.

- **Front Channel (Less Secure)** →  A communication method involving the user agent (browser **redirects**), which is more vulnerable to interception.



<h3 id='the-history' style="font-weight: bold;">OAuth2.1 Introduction and History</h3>

#### So Again What is oAuth ..?


<h3 id='the-flows' style="font-weight: bold;">OAuth Flows</h3>


