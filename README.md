# watsonwork-echo

[![Build Status](https://travis-ci.org/watsonwork/watsonwork-echo.svg)](https://travis-ci.org/watsonwork/watsonwork-echo)

A Node.js sample chatbot app that listens to job related messages posted to a space
in IBM Watson Workspace and echoes them into a designed workspace.

The Watson Work platform provides **spaces** for people to exchange
**messages** in conversations. This app shows how to listen to a conversation
and receive messages on a Webhook endpoint, then send response messages back
to the conversation. It also demonstrates how to authenticate an application
and obtain the OAuth token needed to make Watson Work API calls.

## Try it out

To try the sample app do the following:


### Building the app locally

You can skip this if you've just deployed the app directly to Bluemix.

To build the app in your local development environment, follow these steps:

Install Node.js 6+.

In a terminal window, do the following:
```sh
# For more verbose output
export DEBUG=watsonwork-*

# Get the code
git clone https://github.com/watsonwork/watsonwork-echo

# Build the app
cd watsonwork-echo
npm run build
```

### Registering the app with Watson Work

In your Web browser, go to [Watson Work Services / Apps]
(https://workspace.ibm.com/developer/apps) and add a new app named
**Echo** with a Webhook configured for **message-created** events.

You can download ngrock: https://ngrok.com/ to build your webhooks integration.
Run it locally with `<path>/ngrok http <port>` 

Set the Webhook **Callback URL** to the ngrock url ex `https://044d924f.ngrok.io`and add the app extension:
`https://044d924f.ngrok.io/echo` for example.

Save the app and write down its app id, app secret and Webhook secret.

### Starting the app locally


In the terminal window, do the following:
```
# Configure the app id and app secret
export ECHO_APP_ID=<the Echo app id>
export ECHO_APP_SECRET=<the Echo app secret>
export ECHO_WEBHOOK_SECRET=<the Echo Webhook secret>
```

The Watson Work platform requires Webhook endpoints to use HTTPS. The
sample app listens on HTTPS port 443 and can be configured to use an SSL
certificate like follows:
```
# Configure the SSL certificate
export SSLCERT=<path to your SSL certificate in PEM format>
export SSLKEY=<path to your SSL certificate key in PEM format>

# Start the app
npm start
```

You can also use a different HTTPS port number and a self-signed certificate,
like follows:
```
# Configure the HTTPS port number
export SSLPORT=8443

# Generate a self-signed SSL certificate with /CN set to your server's
# FQDN (fully qualified domain name), www.yourcompany.com for example
openssl req -nodes -new -x509 -keyout server.key -out server.crt -subj "/CN=your server's FQDN"
export SSLCERT=server.crt
export SSLKEY=server.key

# Start the app
npm start
```

If you're running behind a HTTPS proxy, you may want to have the app listen
on HTTP instead to let the proxy handle the HTTPS to HTTP conversion, like
follows:
```
# Configure the HTTP port
export PORT=8080

# Start the app
npm start
```

Finally, if the app is running on your development machine and you don't
want to set up a public IP and domain name for it yourself, you can also
use one the tunnel tools popular for Webhook development like
[localtunnel](https://localtunnel.github.io/www/) or
[ngrok](https://ngrok.com) for example.

Here's how to use a tunnel with localtunnel:

```
# Install the localtunnel module
npm install -g localtunnel

# Set up a tunnel from https://<subdomain name>.localtunnel.me
# to localhost:8080
lt --subdomain <pick a subdomain name> --port 8080

# Configure the app HTTP port
# No need for HTTPS here as localtunnel handles it
export PORT=8080

# Start the app
npm start
```

You can now go back to
[Watson Work Services / Apps](https://workspace.ibm.com/developer/apps),  
edit the **Echo** app and set its Webhook **Callback URL** to
`https://<subdomain name>.localtunnel.me/echo`.

### Enabling the app Webhook

Now that the app is running and listening for HTTPS requests at a public URL,
you're ready to **enable** its Webhook on the Watson Work platform.

Go back to
[Watson Work Services / Apps](https://workspace.ibm.com/developer/apps),
edit the **Echo** app and set its Webhook to **Enabled**. Watson Work will
ping the app Webhook callback URL with a verification challenge request to
check that it's up and responding correctly.

The sample app will respond to that challenge request and output the
following log:
```
watsonwork-echo-app Got Webhook verification challenge
```

### Chatting with the app in a space

You're now ready to chat with the sample app!

Go to [Watson Workspace](https://workspace.ibm.com) and create a space
named **Examples**, then open the **Apps** tab for that space and add the
**Echo** app to it.

In the **Examples** space, say "*Hello there*".

The app will respond with a message echoing what you just said:
"*Hey [your name], did you say Hello there?*"

The application will also output the following log:
```
watsonwork-echo-app Got a message { <message details here> }
watsonwork-echo-app Sent message to space <space id>
```

## Project layout

The sample project source tree is organized as follows:

```sh
README.md     - this README
package.json  - Node.js package definition

src/          - Javascript sources

  app.js      - main app script
  oauth.js    - obtains OAuth tokens for the app
  ssl.js      - configures the app with an SSL certificate

  test/       - unit tests
```

## What API does the app use?

The app uses the [Watson Work OAuth API]
(https://workspace.ibm.com/developer/docs) to authenticate and get an
OAuth token. It implements a Webhook endpoint according to the
[Watson Work Webhook API](https://workspace.ibm.com/developer/docs) to
listen to conversations in a space and receive messages. Finally, it uses
the [Watson Work Spaces API] (https://workspace.ibm.com/developer/docs) to
send back echo messages to the space.

## How can I contribute?

Pull requests welcome!

