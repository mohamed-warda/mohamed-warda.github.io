---
title: Introduction to OAuth 2.1

date: 2024-09-28 8:00:00 +/-0200
categories: [OAuth]
tags: [OAuth2.1, Security, Authorization] # TAG names should always be lowercase
---

![Image](/assets/img/posts/intro_oauth/what_is_oauth.png)








### First, Let's Discuss the Problem ..!

#### Does this image look familiar? (Facebook)  
![Image](/assets/img/posts/intro_oauth/facebook.webp)

If you were around during 2008–2010, when Facebook was rapidly growing, you may have seen this before. At that time, Facebook asked users to enter their Gmail address and password so it could access their contact list and suggest friends who were already on the platform. Do you see the problem with this method?

#### A Clearer Example (Yelp)  
![Image](/assets/img/posts/intro_oauth/yelp.webp)

This is an image of Yelp from around 2008–2011. During sign-up, it would ask you to enter your Gmail address along with your actual password. Yelp used your credentials to log in to your Gmail account on your behalf in order to access your contact list. This allowed Yelp to see which of your friends were already using Yelp and which were not, so you could invite them. Since Yelp was just starting out and had not yet built a strong reputation, many users were hesitant to trust it with sensitive information.

Do you see the problem now? Providing your credentials to another app can be risky. Many people used the same Gmail credentials across various platforms, including banking services, which posed a significant security risk. Sharing login credentials with an unverified site was not only unsafe but also a poor security practice. Although Yelp promised that it would only access your contact list, it still had your password. Moreover, this approach created complications when connecting to third-party apps. For example, if you were connected to both Facebook and Yelp and later decided to remove Yelp, you would have to change your Gmail password—thereby also severing your connection to Facebook.

So here is the big question:  
**How can I let a website access my data without giving it my password?**


### So Now Come the Other Big Question, What is OAuth ..?








Starting with a bit of history is helpful to set the stage and understand where these protocols came from and why they were built the way they are. This background will also help us understand how they are intended to be used today.



### Before OAuth in 2007 The Identity Use Cases We had
- **Simple login** – Forms and cookies
- **Single sign-on across sites** – SAML
	-  SAML was a great protocol for single sign-on, but it was very hard to work with.

### But a New Use Case Emerged 
- **Delegated authorization** 
	-  Delegated authorization is the reason why OAuth was born, as we had poor methods of handling it.
	- The delegated authorization problem: **How can I let a website access my data without giving it my password?**


### Example of the Problem (Yelp)

- When users signed up for Yelp, they had the option to invite their friends by sending emails, which required access to their contact list.

- At the time, Yelp lacked a secure authentication method to handle this process. As a result, it asked users to provide their actual Gmail credentials so Yelp could sign in on their behalf, extract their contact lists, and promised not to do anything else.

- Being a startup, Yelp had not yet built a strong reputation, making users hesitant to trust it with sensitive information.

- Sharing their email credentials was concerning, as many people used their Gmail credentials across various platforms, including banking, which posed a significant security risk. Providing login credentials to an unverified site was not only unsafe but also a poor security practice.

- This raises the question: **How can I allow a website to access my data without giving it my password?** This is where OAuth comes in.

### How to Solve This Problem From a High Level Perspective

- The user initiates the process by clicking a button "Connect to Gmail", which triggers what is known as an **OAuth flow**.  
- A **flow** refers to a series of steps that allow an application to securely access user information.  




# Terminology

- **Resource** → Something you own as a user.  

- **Resource Owner** → The user.  

- **Resource Server** → The server that holds the resource.  

- **User Agent** → The application that runs the client, such as a browser or a mobile app.  

- **Client** → Your web app. From the OAuth server's perspective, it is a client because it requests an access token from the authorization server. The client must implement a callback URL to handle the authorization response.


- **Scope** → Defines the specific data and actions the client (Yelp) is allowed to access. For example, a scope could specify read-only access to contacts or full access to a user’s profile. The **Authorization Server** maintains a predefined list of supported scopes, and the client must request scopes that align with what the server understands and allows.

- **Consent** → The explicit permission the user gives to the authorization server, allowing the client to access their data based on the requested scope.

- **Redirect URL** → The URL where the user is sent after authorization also known as a Callback

- **Authorization Grant** →  this is the result from the  Authorization flow this is what we will use to get access token 

- **Access Token** → A token requested by the client from the OAuth server. The client uses this token when accessing the resource server (external service).  

- **Authorization Server** → The server responsible for generating tokens. It must implement two endpoints:  
	  - **Authorization Endpoint**  
	  - **Token Endpoint**  
	  The OAuth standard requires an authorization server to have these two endpoints.  
	
- **Back Channel (Highly Secure)** → A direct server-to-server communication, safe from user interference or interception.

- **Front Channel (Less Secure)** → A user-involved communication (browser redirects), more exposed to interception.


**Note:** The authorization server and the resource server can be the same server.

![Image](/assets/img/posts/intro_oauth/code_flow.svg)

## Explaining the Code Flow
① First the user interact with his user agent (open his broswer and navigate to the desired site for example `www.client.com` ) this website for example help you archive contacts from you diffrent sources like gmail for example

② so we have a button `Get Gmail Contacts` that get you contacts from gmail and list it so we click it it wil

③ your user agent (browser) will send a get request to the client end point `https://client.com/gmail/contacts`

④ the client will start the auth flow, it will resposne with header location to redirect to the Auth Server to the authorize end point `https://auth.com/authorize`

⑤ your user agent (browser) ill send a get request to the authorize end point including some query param that your auth server need to identify your client (client_id ,client_secret, scopes, redirect_url, code_challange, Code method)

⑥ after hitting the authorize end point at the auth server it will notice your are not authenticated (this will be skipped if ur were logged in) so the authorize end point will return response with redirect 'location' header to your browser

⑦ your browser will send a get request to retrive the login page `https://auth.com/login`

⑧ the auth server returb the logic page as reposne 

⑨ the user will enter his credintails email and password for example, 
note here the oauth2 2.1 dont specify the way/method your use for authintication, it only specify the authorization the steps it required to obtain a token, that is why when u reed about oauth they would alwasy mention authorization 


⑩ the user agent (browser ) will send a post request to the login end point with the use credintails if the credintails was wrong it will be handled returning error or which why its implmentaed  


⑪ if the credintails were correct, as final authentication step the auth server will show a consent screen asking 'Are You Sure you want to give access to the client website ..' and if u clicked no it will return forbided 'or any way it was implented to handle rejection' and the auth flow will stop


⑫ if you clicked `Allow` it will send request with the consent approval  then 

⑬ it will login and create a cookie for example that represent your authenticated session

⑭the login end point will return redirect to the browser to the authorize end point in the auth sever to continio the auth flow

_**Note Here**_ : the consent screen step is optional if your authenticating from your own client u can make the cosent step implicit basicly skipping it 


⑮ the browser will redirect you again to the authorize end point but this time you are authenticated so he will not redirect you to the login page it will generate the `code` 

⑯ the authorize end point will return response to the browser to redirect to the `call back` in the client carring the `code`

⑰ the browser redirect to the client `call back` with the code 

⑱ the client will use `back channal` to do the exchange operations in the `token` end point sending the code and code_verifier to be validated

⑲ the the server return an access token to the clinet to if the code and code_verifier are valid

_**Note Here**_ : I know you now have multiple question like why the auth server return a code why not just return the token, and why i need to send the code_verifier

⑳ the client hold/store the token 

㉑ then the client redirect to the orignal page that the flow started at `https://client.com`
so the client will return resposne with redirect to this poge to the browser

㉒ the browser redirrect and send get request to get the page from the client


㉓ the client return the page to the user agent so that the user can continui his original operation `calling the resource server 'Gmail' to get his contact list`

_**Note Here**_ : at this point the oauth code flow ended

㉔ now the user can continior his original operation and click `Get Gmail Contacts` 

㉕ so the browser will send a get request to the client `https://client.com/gmail/contacts`

㉖ then the client will use the stored token to call the resource server to fetch the contact list

㉗ the resource server return respone with the contacts

㉘ and the client return tha contact to the browerser to be displayed to the user


