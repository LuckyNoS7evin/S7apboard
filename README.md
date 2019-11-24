# S7apboard

## Introduction
S7apboard is a browser based overlay for broadcaster to show messages to viewers over time. This is currently only integrated into streamlabs, we are looking to further our integration down the line.

## Where will this run?
This will need to be run locally or on a web server you own, we will take you through setup and running the project locally however going any further with that is down to you. You can join the discord server https://discord.gg/UWvtCfg if you are looking to talk further about this project.

## Setup
### Step 1. data.json
You will find data.json located in the `/public/` folder. The recommended use is to copy `data.json` and save it as `data.json.local` and only use `data.json.local` while using S7apboard. This stops your secrets being commited if you chose to help improve S7apboard.

This contains all the settings needed to run S7apboard.

1. time_between_flaps - this is the number of seconds between rotation to the next message in the series
1. streamlabs.socket_token - go to [API Settings](https://streamlabs.com/dashboard#/settings/api-settings) on Streamlabs. You will find a tab called `API TOKENS`. When you load this tab you will see two disabled and "hidden" textboxes. Click copy on the `Your Socket API Token` and copy that value into this setting of data.json
1. streamlabs.api_token - go to [API Settings](https://streamlabs.com/dashboard#/settings/api-settings) on Streamlabs. You will find a tab called `API TOKENS`. When you load this tab you will see two disabled and "hidden" textboxes. Click Show on the `Your API Access Token` textbox and copy that value into the `api_token` setting in data.json
1. strings - These are your messages, you can add as many as you like, they can be a maximum of 160 characters. If you want to use Streamlabel items in your messages, you may need to consider the length of that streamlabel item along side the text you want to use. For example Twitch displaynames can be up to 25 characters long. To use Streamlabel items in your messages pick an item from the list below and use it in your message like this `${streamlabels.total_subscriber_count}`

### Step 2 - Node.JS
You will need node installed on your Windows, Mac or Linux system. got to node.js and follow instructions there to get this installed

### Step 3 - Running the applciation

From the root folder you downloaded S7apboard to (the one that contains package.json) run the following commands, once Step 2 is complete.
```
npm install
```
```
npm run serve
```
You should see the following once the system is running correctly
```
DONE  Compiled successfully in 891ms     

  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.1.65:8080/
```

## Streamlabels - possible items

- donation_goal
- most_recent_donator
- session_most_recent_donator
- session_donators
- total_donation_amount
- monthly_donation_amount
- weekly_donation_amount
- 30day_donation_amount
- session_donation_amount
- all_time_top_donator
- monthly_top_donator
- weekly_top_donator
- 30day_top_donator
- session_top_donator
- all_time_top_donators
- monthly_top_donators
- weekly_top_donators
- 30day_top_donators
- session_top_donators
- all_time_top_donations
- monthly_top_donations
- weekly_top_donations
- 30day_top_donations
- session_top_donations
- monthly_subscriber_count
- weekly_subscriber_count
- 30day_subscriber_count
- session_subscriber_count
- session_follower_count
- session_most_recent_follower
- session_most_recent_subscriber
- session_most_recent_resubscriber
- session_subscribers
- session_followers
- most_recent_follower
- most_recent_subscriber
- most_recent_resubscriber
- total_follower_count
- total_subscriber_count
- total_subscriber_score
- monthly_subscriber_score
- weekly_subscriber_score
- 30day_subscriber_score
- session_subscriber_score
- most_recent_cheerer
- session_most_recent_cheerer
- session_cheerers
- total_cheer_amount
- monthly_cheer_amount
- weekly_cheer_amount
- 30day_cheer_amount
- session_cheer_amount
- all_time_top_cheerer
- monthly_top_cheerer
- weekly_top_cheerer
- 30day_top_cheerer
- session_top_cheerer
- all_time_top_cheerers
- monthly_top_cheerers
- weekly_top_cheerers
- 30day_top_cheerers
- session_top_cheerers
- all_time_top_cheers
- monthly_top_cheers
- 30day_top_cheers
- weekly_top_cheers
- session_top_cheers
- all_time_top_sub_gifters
- monthly_top_sub_gifters
- weekly_top_sub_gifters
- 30day_top_sub_gifters
- session_top_sub_gifters
- all_time_top_sub_gifter
- monthly_top_sub_gifter
- weekly_top_sub_gifter
- 30day_top_sub_gifter
- session_top_sub_gifter

