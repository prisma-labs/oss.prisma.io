# Deployment

## Zeit Now

To deploy your `graphql-yoga` server with [`now`](https://zeit.co/now), follow these instructions:

1. Download [**Now Desktop**](https://zeit.co/download)
1. Navigate to the root directory of your `graphql-yoga` server
1. Run `now` in your terminal

> For more detailled info, check out this [**tutorial**](https://blog.graph.cool/deploying-graphql-servers-with-zeit-now-85f4757b79a7).

## Apex Up (AWS Lambda)

[Apex Up](https://github.com/apex/up) is a one-click deployment tool (similar to Zeit Now) which allows to deploy servers/functions to [AWS Lambda](https://console.aws.amazon.com/lambda/home). To deploy your `graphql-yoga` server with `up`, follow these instructions:

1. Download the `up` CLI: `curl -sf https://up.apex.sh/install | sh`
1. Navigate to the root directory of your `graphql-yoga` server
1. Run `up` in your terminal

> For more detailled info, check out this [**tutorial**](https://blog.graph.cool/deploying-graphql-servers-with-apex-up-522f2b75a2ac).

## Heroku

To deploy your `graphql-yoga` server with [Heroku](https://heroku.com), follow these instructions:

1. Download and install the [Heroku Command Line Interface](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) (previously Heroku Toolbelt)
1. Log In to the Heroku CLI with `heroku login`
1. Navigate to the root directory of your `graphql-yoga` server
1. Create the Heroku instance by executing `heroku create`
1. Deploy your GraphQL server by executing `git push heroku master`
