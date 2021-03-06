# <a name="adapters"></a>Adapters

AIVA can simultaneously connect to multiple chat platforms and still behave as one entity. This allows you to **build once, run everywhere**.

It utilizes [hubot's adapters](https://github.com/github/hubot/blob/master/docs/adapters.md). So, feel free to add any as you wish. AIVA comes with **Slack, Telegram, Fb** adapters. See `config/default.json` at key `ADAPTERS` to activate or add more.

For each adapter, we encourage you to create two tokens, for **production** and **development** respectively. The credentials go into `config/default.json`(development), and `config/production.json`.

The webhooks for adapters are auto-set in [`ngrok`](#ngrok) in `src/aiva.js`; you don't even need to provide a webhook url.

As mentioned, we can use the custom message formatting of a platform, such as Slack attachment or Facebook rich messages. You can check the adapter by `robot.adapterName` to format message accordingly, then send it using the method `robot.adapter.customMessage` for any adapter. More below.

<aside class="notice">
customMessage will be generalized soon - we are still working with the developers of the adapters, and will then provide better in-code examples.
</aside>


## Slack

[hubot-slack](https://github.com/slackhq/hubot-slack) comes with AIVA. It uses Slack's RTC API. Create your bot and get the token [here](https://my.slack.com/services/new/bot).

To use customMessage, see the [example (pending update)](https://github.com/slackhq/hubot-slack/issues/170#issuecomment-113315455), invoked when `robot.adapterName == 'slack'`. Refer to the [Slack attachments](https://api.slack.com/docs/attachments) for formatting.


## Telegram

[hubot-telegram](https://github.com/lukefx/hubot-telegram) comes with AIVA. Create your bot and get the token [here](https://core.telegram.org/bots#3-how-do-i-create-a-bot).

<aside class="notice">
It's optional to provide a webhook url in config/ with TELEGRAM_WEBHOOK; it is set up automatically in <a href="#ngrok"><code>index.js with ngrok</code></a>.
</aside>

To use customMessage, see the [example](https://github.com/lukefx/hubot-telegram#telegram-specific-functionality-ie-stickers-images), invoked when `robot.adapterName == 'telegram'`. Refer to [Telegram bot API](https://core.telegram.org/bots/api) for formatting.


## Facebook

[hubot-fb](https://github.com/chen-ye/hubot-fb) comes with AIVA. Create your bot by creating a Fb App and Page, as detailed [here](https://developers.facebook.com/docs/messenger-platform/quickstart). Set the `FB_PAGE_ID, FB_APP_ID, FB_APP_SECRET, FB_PAGE_TOKEN` as explained in the adapter's page [hubot-fb](https://github.com/kengz/hubot-fb#configuration).

<aside class="notice">
It's optional to provide a webhook url; it is set up automatically in <a href="#ngrok"><code>aiva.js with ngrok</code></a>.
</aside>

<aside class="warning">
Note that Facebook takes 10 mins to update webhook, so you may need to wait till then to start receiving messages. We advice using a persistent url in config/ key FB_WEBHOOK, e.g. a Heroku url, or ngrok custom domain url; see <a href="#ngrok"><code>Tips on Webhook with ngrok</code></a>.
</aside>

To use customMessage, see [the examples](https://github.com/chen-ye/hubot-fb#sending-rich-messages-templates-images), invoked when `robot.adapterName == 'fb'`. Refer to the [Send API](https://developers.facebook.com/docs/messenger-platform/send-api-reference) for formatting.

