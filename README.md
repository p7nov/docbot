# DocBot - Tarantool Documentation pipeline bot

[![Build Status](https://travis-ci.org/tarantool/docbot.svg?branch=master)](https://travis-ci.org/tarantool/docbot)

TarantoolBot automates a process of creating a documentation request after
external behaviour is changed. Documentation bot has its own account on mail.ru
and on github.com. Its name is TarantoolBot. It uses GitHub webhooks to track
all new comments on issues and commits. In a comment or a commit message a one
can ask the bot to create a new issue on tarantool/doc, when the original issue
will be closed or the commit will be pushed, using the special template. The one
must mention the bot, write a title and description:
```
@TarantoolBot document
Title: one-line title
Description that can be multiline,
contain markup and links.
```
When the bot sees its mentioning, it checks the request syntax and writes result
of request parsing into its web [journal](https://tarantool-docbot.herokuapp.com).

If the request had been done via issue comments, then the bot responds with
either error description or with `@<CommentAuthor>: Accept` right into the issue
comments. When the issue is closed, the bot finds this comment, extracts the
title and description, and creates a new issue on tarantool/doc with notifying
the comment author and the original issue/commit.

When the request is in the commit message, a one should write the request at the
end of the message. Even after `Closes/Fixes etc` GitHub hits. All below the
`@TarantoolBot document` is treated as a request.

Thanks to the bot, now no one must care about creating a doc issue after push
into trunk, changing some external behaviour.

## Deployment

Build Docker container with:

```sh
docker build -t docbot .
```

Then run it like this:

```sh
docker run \
    -d -p5000:5000 \
    -e GITHUB_TOKEN=<token> \
    -e GITHUB_SIGN_KEY=<sign_key> \
    -e PROMETHEUS_TOKEN=<token> \
    --name docbot docbot
```

To check that it works try to get `localhost:5000` - it will print the
**TarantoolBot Journal** message, if all is ok. After this set a GitHub hook in
the repository, that you want the bot tracks. The hook must send notifications
about new comments in all issues, about closed issues, log all events onto its
webpage.
