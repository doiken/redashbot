# Slack Bot for Redash V2

[日本語README](https://github.com/yamitzky/redashbot/blob/main/README.ja.md)

Redashbot V2 is a open-source slack bot for [Redash](https://redash.io).

(This project was forked from hakobera/redashbot, but it is not maintained anymore. I have rewritten almost all of the code and published as v2.)

## Features

- Visualization(Chart) screenshot
- Dashboard screenshot
- Table result (*NOT SCREENSHOT*)
- Docker deployment
- <s>Serverless deployment</s>
- HTTP-based New Slack app (non-RTM style)
- **Open source!**

![screenshot.png](./images/screenshot.png)


## Usage

- Visualization
  - `@botname <Query URL>#<Viz ID>`
    - e.g. `@redash https://your-redash-server.example.com/queries/1#2`
- Dashboard
  - `@botname <Dashboard URL>`
    - e.g. `@redash https://your-redash-server.example.com/dashboards/dashboard-name`
- Table
  - `@botname <Query URL>#table`
    - e.g. `@redash https://your-redash-server.example.com/queries/1#table`

You can add the following additional arguments to the command.

- e.g. `@botname https://your-redash-server.example.com/queries/1#2 rb_width=1280`

| key | description | default |
| --- | --- | --- |
| rb_width | Specify viewport width | 1024 |
| rb_hight | Specify viewport height | 360 |

## Setup

[Create a Slack app](https://api.slack.com/apps/) and set environment variables `SLACK_BOT_TOKEN` and `SLACK_SIGNING_SECRET`.

[The Official Document](https://slack.dev/bolt-js/tutorial/getting-started#create-an-app).

On Event Subscription page, `Request URL` will be `https://<your-domain>/slack/events`.

Then, `npm start` or `docker run yamitzky/redashbot:main` to start. When you use Docker, do not forget to pass environment variable via `-e` or `.env` file.

### Slash Command (Optional)

You can use redashbot with `/redash-capture [URL]`.

On Slash Command page of your app, click [Create New Command] and submit. `Command` must be `/redash-capture` and `Request URL` will be `https://<your-domain>/slack/events`.

### Workflow Steps (Optional)

You can use redashbot as a Workflow step.

On Interactivity & Shortcuts page of your app, enable Interactivity. `Request URL` will be `https://<your-domain>/slack/events`.

Then, move to Workflow Steps page, and click [Add Step] and submit. `Callback ID` must be `redash_capture`.


## Environment variables

### SLACK_BOT_TOKEN (required)

Slack's bot token.

### SLACK_SIGNING_SECRET (required)

Slack's sigining secret.

### SLACK_SOCKET_MODE (optional)

Set to `true` to enable Socket Mode. When enabled, the bot will use WebSocket connections instead of HTTP endpoints.

### SLACK_APP_TOKEN (required for Socket Mode)

App-level token for Socket Mode. Required when `SLACK_SOCKET_MODE=true`. The token should start with `xapp-`.

### PORT (optional)

Port number for the HTTP server. Default is 3000.

### REDASH_HOST and REDASH_API_KEY (optional)

Redash's URL and its API Key.

## REDASH_HOST_ALIAS (optional)

Redash' URL accessible from the bot.

### REDASH_HOSTS_AND_API_KEYS (optional)

If you want to use multiple Redash at once, specify this variable like below

```
REDASH_HOSTS_AND_API_KEYS="http://redash1.example.com;TOKEN1,http://redash2.example.com;TOKEN2"
```

or if you need to specify REDASH_HOST_ALIAS for each Redash, like below

```
REDASH_HOSTS_AND_API_KEYS="http://redash1.example.com;http://redash1-alias.example.com;TOKEN1,http://redash2.example.com;TOKEN2"
```

### SLEEP_TIME (optional)

Milliseconds to wait loading finished before capturing.

### BROWSER (optional and experimental)

`chromium`, `firefox` or `webkit`. default is `chromium`

### BROWSER_TIMEOUT (optional)

Browser timeout in milliseconds. Default is the value of `SLEEP_TIME` or 10000 if `SLEEP_TIME` is not set.

### REDASH_CUSTOM_HEADERS (optional)

Add custom HTTP headers to Redash requests. Specify in a semicolon-separated key:value format.

Example:
```
REDASH_CUSTOM_HEADERS="CF-Access-Client-Id:your-client-id;CF-Access-Client-Secret:your-client-secret"
```

## How to develop

Clone this repository, then

```bash
$ npm install
$ npx playwright install 
$ export REDASH_HOST=https://your-redash-server.example.com
$ export REDASH_API_KEY=your-redash-api-key
$ export SLACK_BOT_TOKEN=your-slack-bot-token
$ export SLACK_SIGNING_SECRET=your-slack-signing-secret
$ # For Socket Mode (optional)
$ export SLACK_SOCKET_MODE=true
$ export SLACK_APP_TOKEN=xapp-your-app-token
$ npm start
```

## Deploy

Redashbot is just a node program.

```
npm start
```

### Docker

[Docker image](https://hub.docker.com/r/yamitzky/redashbot) is provided. Currently, `latest` tag is used for v1(old), then you must use `2.0.0` or something like that.

```
docker run -it --rm -e SLACK_BOT_TOKEN=$SLACK_BOT_TOKEN -e SLACK_SIGNING_SECRET=$SLACK_SIGNING_SECRET -e REDASH_HOSTS_AND_API_KEYS=$REDASH_HOSTS_AND_API_KEYS -p 3000:3000 yamitzky/redashbot:2.0.0
```

docker-compose is also provided.

```
docker-compose up
```

### Heroku (NOT TESTED!!!!!!!!)

You can easy to deploy redashbot to Heroku, just click following button.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)
