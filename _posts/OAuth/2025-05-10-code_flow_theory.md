---
title: OAuth Part 2 - Authorization Code Flow
date: 2025-09-9 8:00:00 +/-0200
categories: [OAuth]
tags: [OAuth, Security, Authorization] # TAG names should always be lowercase
image: /assets/img/posts/code_flow/redirect.jpg
---
the blog flow
1. why starting with the flow 
2. introduction to the specifications هتلكم عن كل جزء لحد الرابع
3. اقول نبذه علي الفلو من غير اللوجن
4. اشرح كل جزء من الفلو الصغير ده من الدوكس 
5. واشرح الاتاكس لكل جزء برده
6. صوره الفلو الكبير
7. اكسر الفلو الكبير

✅ 1. Authorization Code Interception Attack
What is it?
An attacker intercepts the authorization code while it’s being redirected to the client.

Prevention via Code Flow (with PKCE):

PKCE (Proof Key for Code Exchange): The client sends a code_challenge with the authorization request and a code_verifier when exchanging the code for a token.

The token endpoint checks if they match, preventing use of intercepted codes by attackers.

✅ 2. Redirect URI Manipulation
What is it?
An attacker changes the redirect URI to steal the code or tokens.

Prevention via Code Flow:

The authorization server only redirects to pre-registered redirect URIs, avoiding redirection to malicious URLs.

It should strictly match the redirect URI to the one registered.

✅ 3. CSRF (Cross-Site Request Forgery)
What is it?
An attacker tricks a user into making an unwanted authorization request.

Prevention via Code Flow:

Use of a random state parameter to bind the authorization request to the user’s session.

The client must verify the state value in the redirect to prevent CSRF.

✅ 4. Token Leakage via Front Channel
What is it?
Tokens (especially access tokens) are leaked in the browser URL or referer headers.

Prevention via Code Flow:

The access token is never exposed in the front channel (browser), unlike the Implicit Flow.

Tokens are only sent via secure backchannel communication.

✅ 5. Phishing Attacks
What is it?
Users are tricked into providing credentials to fake login pages.

Prevention via Code Flow:

OAuth doesn't directly prevent phishing, but using OpenID Connect with trusted identity providers and educating users helps.

FIDO2/WebAuthn can also reduce this risk when used with OAuth.

✅ 6. Replay Attacks
What is it?
An attacker reuses an old authorization code or request.

Prevention via Code Flow:

Authorization codes are one-time use only and expire quickly.

The use of PKCE ensures that even reused codes without a valid code_verifier will be rejected.

✅ 7. Client Impersonation
What is it?
An attacker impersonates a legitimate client to get tokens.

Prevention via Code Flow:

Confidential clients must authenticate with client_id + client_secret at the token endpoint.

Public clients use PKCE to provide proof of origin.

✅ 8. Misuse of Access Token
What is it?
An attacker uses a token issued for one client (or scope) in another context.

Prevention via Code Flow:

Scopes and audience are strictly validated by the resource server.

Use JWT validation to ensure tokens are issued for the correct client/resource.
-------------------------------------- stope here
<h3 id='the-INTRO' style="font-weight: bold;">Introduction .. Why Starting With Authorization Code Flow ..?</h3>

this flow is the most known flow and any one try to explain oauth start with it as it have the most steps, its very similier to the `implicit flow` but some added steps for secuirity, this flow is 90% of cases this would be the flow to go to , if you understand this flow all the other flow would be easy to pick up the flow is the flow with the most steps and all the terminology you will find it it the other flow would be basicly less steps with some tweks according to its use


<h3 id="the-INTRO" style="font-weight: bold;">
  Let's look into the specification for OAuth 2.0 
  <a href="https://datatracker.ietf.org/doc/html/rfc6749" target="_blank" rel="noopener noreferrer">
    [Flow RFC]
  </a>
</h3>
- The first part is the introduction, where all the terminology is explained.

![Image](/assets/img/posts/code_flow/rfc_introduction.PNG)

- The second part covers client registration. When you want to use an authorization server and register your client (e.g., adding "Login with Google"), Google provides you with a **client ID** and **client secret**. You also need to specify the **redirect URL**.

![Image](/assets/img/posts/code_flow/rfc_client_registration.PNG)
## Wrong check again
- The third part discusses the endpoints we need to implement but focuses more on the **token endpoint** rather than the **authorization endpoint**. This section provides the base implementation, while section 4 contains the additional details required for each flow, as seen in `4.1.3 Token Endpoint Extension`.

![Image](/assets/img/posts/code_flow/rfc_auth_enpoints.PNG)

- The fourth part describes the different authorization flows and how to obtain an access token and the implementation

![Image](/assets/img/posts/code_flow/rfc_auth_flows.PNG)








- _**Note:**_ For More Clarification Think about the "Client" as MVC Application the return view as it do actions

 
![Image](/assets/img/posts/code_flow/code_flow.svg)



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

