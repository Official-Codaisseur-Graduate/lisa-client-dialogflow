# What this repository is about

This repository is the version control system of the current settings in Dialogflow. It is one of the three parts of the LISA Voice Assistant Project among the client and server repositories. Dialogflow is meant to be used by Vitalis elderlies to ask for the menu of the nearby kitchen(s). User voice input sends requests to and fetches data from the backend server.

This readme file will introduce the fundamental concepts of DialogFlow and walk you through the installation process.

## Table of contents:

- **[Repositories in the LISA-project](#repositories-in-the-LISA-project)**
- **[What is Dialogflow](#what-is-dialogflow)**
- **[Installation](#installation)**
- **[How to add changes](#how-to-add-changes)**

## Repositories in the LISA-project

NOTE: Read the Docs for set-up and the latest status of the code - make sure to use the correct branch (development or any open branches that hasn’t been merged into development yet). Only merge into the master branch if all the codes in development are working AND you have already checked with Rein.

- [LISA-server](https://github.com/Official-Codaisseur-Graduate/lisa-server)
- [LISA-client](https://github.com/Official-Codaisseur-Graduate/lisa-client)
- LISA Dialogflow - you are here!

# What is Dialogflow
  
Dialogflow (formerly Api.ai) is a Google-owned developer of human–computer interaction technologies based on natural language conversations. You can develop a virtual buddy for Android, iOS, and Windows Phone smartphones that performs tasks and answers users’ questions in a natural language. It also created a natural language processing engine that incorporates conversation context like dialogue history, location and user preferences.

If you intent to understand and improve on the Dialogflow features of Project Lisa, I recommend a short YouTube series to get you started:

- [Part 1: Intents](https://www.youtube.com/watch?v=9aHusGxntPw)
- [Part 2: Entities](https://www.youtube.com/watch?v=kzdL6GxJ_WY)
- [Part 3: Dialog control](https://www.youtube.com/watch?v=-tOamKtmxdY)

## Agents

An Agent is a specific virtual buddy, chatbot or skill. In this case, LISA. There is an invocation to start it: “Hey Google, praat met de keuken” or “Hey Google, talk to the kitchen” will trigger “Hallo, ik ben Lisa. Wat is uw vraag?”.

[Edit invocation here if need be](https://console.actions.google.com/u/1/project/vitalis-lisnji/invocation/)

## Intents

An intent maps what a user says with what your agent does. To fetch the menu, LISA has the intent ‘fetch_menu’.

Example questions:
* “Wat eten we vanavond?”
* “Wat staat er morgen op het menu?”
* “Wat is het toetje vandaag?”
* “Wat is dinsdag het hoofdgerecht?”

Date and course information are sent along with the request to the server. If none are specified (“Wat eten we?”), the backend route will assume full meal for today. 

## Entities

An Entity is a property which can be used by Dialogflow to extract values from the user request. It filters information from natural speech, and transforms it to a standardized format. LISA uses two entities:
* @sys.date-time, date, a standard Dialogflow entity 
* @gerecht, type, a custom entity

## What is Fulfillment?

Dialogflow receives a request from the user (along with the entity values to be sent with the request) and now needs to request the information from the database to fulfill the user request. Now this data will be sent to our **webhook** so that the required information can be fetched (this will be dependent on your implementation). Once the webhook has fetched our required information it will send it back to Dialogflow so that it can be presented to the user in the desired manner.

![webhook-fulfimment.png](./webhook-fulfimment.png)

## How Dialogflow connects with Backend App

![dialoglow-to-backend.jpg](./dialoglow-to-backend.jpg)

## What is a Webhook?

When you are working with custom backend like Node.js with Express, you need to:
* turn on the option Enable webhook, and
* call for slot filling in the Fulfillment section of the intent.

![Webhook option.png](./Webhook-option.png)
![webhook-api.png](./webhook-api.png)

[Dialogflow docs](https://cloud.google.com/dialogflow/docs/)

## Installation

1) First, make sure to set up the backend [server](https://github.com/Official-Codaisseur-Graduate/lisa-server) if you didn't already

2) Download Zip File

   There is no need to install anything. Simply download the zip so that it will be used to quickly set up Dialogflow for you.
   
   <b>For development purpose</b>:
   
   [Download](https://codeload.github.com/Official-Codaisseur-Graduate/lisa-client-dialogflow/zip/development) the Dialogflow client developer as zip.
   
   <b>For production purpose</b>:
   
   [Download](https://github.com/Official-Codaisseur-Graduate/lisa-client-dialogflow/archive/master.zip) the Dialogflow client master as zip.

3) Dialogflow needs to connect to your backend server through a *public* URL, meaning that e.g. localhost:4000 simply won't work. There are three ways to make your local server public, using either Serveo, Localtunnel or a Heroku link<br>
     * To use **Serveo**, run this in your terminal window (it should give you an url):
       ```
       $ ssh -R 80:localhost:5000 serveo.net 
       ```
       You might need to type `yes` if a warning is thrown. The https link in green is your server URL<br>
       <br>
       Serveo is a nice tool to use while developing: https://serveo.net/
       It streams your server to the adress Serveo sets up for you. You can use this adress in Dialogflow -> fulfillment.
       The link look likes this:(this one does not work)
       https://dialogflow.cloud.google.com/#/agent/`<random numbers and letters>`/fulfillment
       <br>
       <br>
     * If Serveo doesn't provide you with an url, you can try **Localtunnel**. To use LocalTunner:<br>
       Install Localtunnel globally (requires NodeJS) to make it accessible anywhere:
       ```
       npm install -g localtunnel
       ```
       Start a webserver on some local port (eg http://localhost:5000) and use the command line interface to request a tunnel to your local server:
       ```
       lt --port 5000
       ```
       You will receive a url, for example https://gqgh.localtunnel.me, that you can share with anyone for as long as your local instance of lt remains active. 
   
     * Either way, when you receive a url, go to Dialogflow -> Fulfillment tab on the left -> copy and paste it in the Webhook URL while *always* ending with the endpoint /google-menus (e.g. https://gqgh.localtunnel.me/google-menus). Any requests will be routed to your local service at the specified port.
   
       Later when you test Dialogflow in the test environment, you may receive this error "MalformedResponse". When you do, you need to retrieve a new url and update the webhook url before you test again.

4) Connect LISA to your local database:<br>
     <br>
     For privacy reasons, it's best to **use your personal Google account and create a new agent** for development purpose:
     * Log out of all your Google Accounts and delete the information in your local storage before only logging in with your personal Google account to make sure you continue the next steps with the right account.
     * Go to [Dialogflow](https://dialogflow.cloud.google.com/) and log in. **NOTE** Dialogflow logs in with the Google account you are logged in with automatically. Make sure this is with your personal account.
     * Create a new agent (top left of the page). Besides adding a name, you should see the option to set the default language. Make sure the default language is *Dutch*. If you don't see this option. Refresh page and try again.
     * Once a new agent is created, go to the settings of the agent (the wheel next to your agent's name)
     * Click on *Export and Import* and import the zip file from step 2. Don't forget to type *IMPORT* in the input form beneath it.
     * Enable Webhook in the fulfullment tab and fill in `<your local server URl>/google-menus` as URL<br>
     * To integrate the agent with the Google Assistant, click on the *integrations* tab on the left of the page, and go to Google Assistant.
     * Add the intents by clicking on the form *add intent*. The intents that come with the zip file should show up as a drop down menu. Select all of them.
     * When you are finished, click on *Test*. If everything is set up correctly, you will be directed to your Google Actions test environment.
     * The agent you created is a new project in your [Google Actions](https://console.actions.google.com). Go to Google Actions and click on the name of the agent you created.
     * Go to *Decide how your action is invoked*.
     * Set up an invocation, preferrably 'de Kok' and save it. **NOTE**: if your language setting in your Google account isn't Dutch by default (or if you get an error), switch off the box *Match user's language setting*. In the *Google Assistant Voice* part, you should see *(NL-NL)* in the voice form. In addition, after you hit "save" the form may throw an error saying the name is invalid. This may be a bug. As long as you see the pop-up 'Invocation details saved successfully', move on.
     * Make sure to set the language to Dutch (NL).
     * Testing: First input 'Praat met de Kok', after you get a response, try 'Wat eten we vanavond'. If you get an error "MalformedResponse", refresh your webhook url.
     * In your [MyAccount](https://myaccount.google.com) page, navigate to privacy & personalization, Web/App activity and history must be turned on in order to have the location feature working.
     * Test Again (Go to *Test* in the header of the page) <br>
     <br>
     <b>Possible Errors:</b><br>
     <b>The testing envrionment will not load:</b> 
     Does the testing not work, and you receive a <em>Permisson access denied</em> error? Most likely the accounts on Dialogflow and your Google Actions are not the same. You can check this by going to the settings of your agent in Dialogflow. Click on the *Share* tab, and you see the Google Account of the person who has access to the agent. If this is not your personal account remove the agent, and start again and make sure to be logged out and clean your local storage first before trying again.
     <br>
     <br>
     <b>Test envrionment works, but the menu isn't fetched?</b> 
     If you are in your testing environment, but the fetch-menu intent doesn't work, fetch a new Serveo or Localtunnel link and connect that as your webhook.
     

5) You can test the app with Google Assistant on your device as long as you're logged in with your personal Google account. Ask 'Hey Google, praat met de kok' which means 'Hey Google, talk to the chef'.

6) Ask what the menu is for the day you made some items for (e.g. 'Wat eten we vanavond?'). You can also ask for just the starter, main-course, or dessert for a specific day.

## How to add changes
<b>WARNING:</b> [this](https://dialogflow.cloud.google.com/#/agent/e4ee0583-d68d-4127-a8bd-49f3522ded28/intents) is the account used in production. Only make changes if a new backend version is deployed, otherwise you will break it<br>
  **Production Account**<br>
  Google ID: lisa.vitalis.assistant@gmail.com<br>
  For the password, please contact Rein<br>

If you made changes to the lisa dialogflow client and are satisfied with the outcome, make sure to:
   * restore the fulfillment url to `https://immense-fjord-88389.herokuapp.com` (this is the back-end that's in development. It may be outdated, so check to make sure)
   * export the settings as zip [here](https://dialogflow.cloud.google.com/#/editAgent)
   * unpack the zip in the root directory you cloned this repository in
   * ```
      $ git commit
      $ git push
      ```
If you messed something up, you can just clone this repository as zip from github and upload it to the dialogflow client.








