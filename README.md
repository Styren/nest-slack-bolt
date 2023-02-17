<p align="center">
  <a href="https://github.com/bamada/nest-slack-bolt" target="blank"><img src="logo.svg" width="100" alt="Nestjs Slack Bolt" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

<p align="center">A <a href="http://nestjs.com/" target="blank">Nestjs</a> module to interact with <a href="http://nodejs.org" target="_blank">Slack</a> API using <a href="https://api.slack.com/bolt">Bolt</a> SDK</p>

[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat-square)](https://github.com/prettier/prettier)

---

## Description

This module gives a handy way to develop Slack applications using the Nestjs framework

## Features

- Handle Messages
- Handle Commands
- Handle Actions
- Handle Events

## Installation

```bash
# yarn
$ yarn add nestjs-slack-bolt

# npm
$ npm i nestjs-slack-bolt
```

## Usage

Add these variables to the `.env` file

```bash
# to define on API mode
SLACK_SIGNING_SECRET="**"

# to define on Socket mode
SLACK_APP_TOKEN="**"

# require variables
SLACK_BOT_TOKEN="**"
SLACK_SOCKET_MODE=true

```

Import the `SlackModule`

```typescript
import { Module } from '@nestjs/common';
import { SlackModule } from 'nestjs-slack-bolt';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [SlackModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## Example

Using annotation

```typescript
import { Controller } from '@nestjs/common';
import { Action, Command, Message, Event } from 'nestjs-slack-bolt';
import { AppService } from './app.service';
import {
  SlackActionMiddlewareArgs,
  SlackCommandMiddlewareArgs,
  SlackEventMiddlewareArgs,
} from '@slack/bolt';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Message('hi') //Handle a message event
  message({ say }: SlackEventMiddlewareArgs) {
    say('Hello');
  }

  @Action('click') //Handle an action
  action({ say }: SlackActionMiddlewareArgs) {
    say('click event received');
  }

  @Command('/list') // handle command
  command({ say }: SlackCommandMiddlewareArgs) {
    say('/list command received');
  }

  @Event('app_home_opened')
  event({ say }: SlackEventMiddlewareArgs) {
    say('app_open_event received');
  }
}
```

## Example

Using the SlackService

```typescript
import { Controller } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { SlackService } from 'nestjs-slack-bolt/services/slack.service';
import { Cron, CronExpression } from '@nestjs/schedule';
import { from } from 'rxjs';

@Controller()
export class AppController {
  constructor(
    private readonly slackService: SlackService,
    private readonly configService: ConfigService,
  ) {}

  @Cron(CronExpression.EVERY_10_SECONDS)
  cronJob() {
    this.searchUsers()
      .pipe()
      .subscribe((users) => {
        console.log(JSON.stringify(users));
      });
  }

  searchUsers(cursor?: string) {
    return from(
      this.slackService.client.users.list({
        token: this.configService.get('SLACK_BOT_TOKEN'),
        limit: 1,
        ...(cursor && { cursor }),
      }),
    );
  }
}
```

## TODO

- Improve testing
- handle additional slack events

## Contribute & Disclaimer

....
