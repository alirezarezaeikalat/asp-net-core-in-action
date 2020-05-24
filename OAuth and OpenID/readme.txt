1. pattern for using oauth:

    yelp(example of app)            ----->                  accounts.google.com    
    (connect with google)      (client give Redirect 
                                1.URI and                   (user and pass for google)
                                2.response type: code           |
                                3.it also give the scope        |
                                4.and client id and client      |
                                key)                            |           
                                                                |
                                                                |
                                                                |
                                                    google notif, that yelp wants to 
we go to the yelp.com/callback      <------         access your ...
    (in this address we have
    access to info that we want)                       (yes or no)   

2. oauth terminology:

    a. Resource owner: it is fancy word for user
    b. Client: is the app that want to use information (like yelp in this app)
    c. Authorization server: in this example is accounts.google.com
    d. Resource server: in this example is google contact API
    e. Authorization grant: it is the grant that we get when we click on yes
    f. Redirect URI: it is the callback that we go to after giving the access
    g. Access token: the client after getting the permission, get this to 
                        do whatever it want to do.
    h. scope: this is define, how much info that client can have this scope is
                        going to be in the access token.

[ATTENTION]
in callback URI, the handler function, Exchange authorization code for 
access token.

3. There is reason that we first get a code and then exchagne it in the callback URI
    with access token. in here we are going to explain this: 

    we have two channel in network that we can transfer data in it:

        a. back channel (highly secure channel): request from a back end of server
            to another sever in https protocol 
        b. front channel (less secure channel): request from a browser

    in the process of oauth we use both of this, all of the process is in front
    channel (even the authorization server sends the code in the callback url
    inside query string), except the proccess of changing the code for access token.
    in this proccess your backend server, sends code to authorization server and 
    get the access token.

4. The reason that back channel is safe, it is because of that besides the code 
    that authorization server give us, our server has a code (for example a client
    ID and client secret) that we have to sends it in the backend beside the code,
    and this api key is neccessary for transfering data between severs.


5. oauth flows: 

    a. Authorization code: this is the one that we've described (front channel + back
    channel)

    b. Implicit (front channel only) this is like fully react app, because you don't
    have backend. (we add response type: token)

    c. Resource owner password credential (backchannel only)

    d. Client credential (back channel only): for machine to machine 

6. As you can see, oauth is for authorization, not authentications, in oauth we 
   check for the permission and scope to access something, and it is not designed
   for authentication and adding a user. so OpenID is another layer on 
   oauth 2.0 for making it useful for authentication:

   the flow of OpenID is just like oauth authorization code, but the scope it 
   will changed to openid profile. and after sending the authorization code,
   we also get ID token(JWT) as well as access token, and the client (application) 
   can use id token to find out who is the user. and we can use access token 
   to get even more info about the user.


7. Using openid in a typical website that has separate identity:

    when you request for login to the identity (authorization server) it gives back
    access token and token id and you can set the id token information to 

8. for native mobile apps, we have to use openID connect code flow beside 
    PKCE (proof key for code exchange) there is library app off that abstract all this
