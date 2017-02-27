# Solving Cross-Origin-Relationship Problems
This will help you if you are testing a URL successfully in Postman, but your application will not successfully make your request.  You will get an error in your console that looks like this: 
```
XMLHttpRequest cannot load https://itunes.apple.com/search?term=beyonce&media=music. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access.
```
or like this:
```
Blocked loading resource from url not allowed by $sceDelegate policy.  URL: https://itunes.apple.com/search?term=beyonce&media=music
```

There are a few ways to fix this problem.

## Short Answer - Chrome Extension
You can download a Chrome extension that enables CORS on your browser.  This is a great quick fix, or a great testing tool to make sure that you're correctly diagnosing the problem.  You can access the extension from this link:

https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi

This will add a 'CORS' icon to your extensions that you can access to enable/disable CORS to your browser.  (Note: When active it may cause others error authenticating with Google, Facebook, etc.)

##Long Answer - Proxy Server
You can setup a proxy server in another repository to act as a 'middleman' between the api you're trying to access and your application.  This will require a new repository.  You can access a boilerplate for this repository here: 

https://github.com/BlaiseRoberts/proxy-server

When creating the repository on gitHub you can have it create a .gitignore for you for a 'Node' project.

1. Once the repository is created with your .gitignore file you will need to run some commands in your terminal to set up the rest of your files.
    ```
    touch server.js
    npm init
    npm install express --save
    npm install request --save
    ```
This should create a .js file and a package.json file that should include express and request in your dependencies.  

1. Now you will be able to copy some code from the boilerplate repository.  From the package.json file you will need to include an 'engine' it will be inserted between your "main" and "scripts" and look like this:
    ```
    "engines": {
        "node": "6.5.0"
    },
    ```
(for more details refer to the boilerplate code)
From the server.js file you will need to copy the entire file and then next we will edit some of the code.

1. After you've copied the server.js file you'll only need to change a few things. You will need the URL of the api your application is trying to access(externalAPI) and a path name that you will create to facilitate the request. ('/made/up/path')
    You will replace code within the 'app.get' and it should look like this:
    ```
    app.get('/made/up/path/*', (req, res) => {
      let apiCall = req.url.slice('/made/up/path/'.length)
      let apiReq = `externalAPI${apiCall}`
      request.get(apiReq, (err, _, body) => {
        res.send(body)
      });
    });
    ```
Once this is complete you can test your proxy-server by hosting it locally.  You can run this command in your terminal:
    ```
    node server.js
    ```
This should give you a localhost for your proxy server on a port.  It will give a message like: 
    ```
    Listening on port: 6060
    ```
You can now test our new URL in Postman.  It should look something like this:
    ```
    localhost:6060/api/itunes/?term=beyonce&media=music
    ```
(You will have to use your port number and your 'made/up/path', these will be replaced by the beginning or your original external api URL on your prox-server)

    **Woo-Hoo IT WORKS!** 

    *(I hope)*

    **Now to server your proxy-server up somewhere outside of your terminal!**

1. Create a Heroku account!

This will invole activating your account via your personal e-mail.
Once your have created your account you will need to install heroku to your machine via homebrew.  You should type this command into your terminal:

    ```
     brew install heroku
    ```

After you have installed heroku we can setup a remote repository on heroku.
You should click create new app and follow the instructions.  Next, you should type the commands below once inside your proxy-server project:

    ```
    heroku login  //Login with your user info.
    heroku git:remote -a test-1111 //'test-1111' replace with your repo's name
    git add .
    git commit -am "make it better"
    git push heroku master
    ```

Once this is finished it should give you a message in your terminal that will include your new URL or accessing your proxy-server. This end of your message should look something like this:

    ```
    remote: -----> Compressing...
    remote:        Done: 14.4M
    remote: -----> Launching...
    remote:        Released v3
    remote:        https://itunes-proxy.herokuapp.com/ deployed to Heroku
    remote: 
    remote: Verifying deploy... done.
    To https://git.heroku.com/itunes-proxy.git
    ```

You will only need the URL under 'Launching...' 
(ex: 'https://itunes-proxy.herokuapp.com/')
You can now test your new address in Postman as well. it should look something like this:

    ```
    https://itunes-proxy.herokuapp.com/api/itunes/?term=beyonce&media=music
    ```

We are using the Heroku URL + made/up/path + Params for external api.

__WOO HOO!__
__You really did it!__

Now you can use this new URL in your application to send httprequests to the external api!