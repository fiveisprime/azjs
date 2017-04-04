# Az.js CLI

Az.js is a command line tool which attempts to provide an opinionated (and hopefully simpler!) developer experience for deploying and managing Node.js based apps running on Azure. It is meant to experiment/play around with ideas to reduce the barrier of entry to cloud development, and therefore, is a compliment to the already awesome [Azure CLI 2.0](http://github.com/azure/azure-cli) (which supports a significantly richer set of scenarios and capabilities).

This CLI is very much inspired by the amazing work done by other tools such as [Docker](http://www.docker.com), [Now](http://zeit.co/now) and [Serverless](http://serverless.com), which are making great strides in the pursuit of simplicity, and are benchmarks for what CLI-driven workflows can and should look like moving forward.

<img src="https://cloud.githubusercontent.com/assets/116461/24490140/129080e0-14d6-11e7-93b9-b5175c1e131f.png" width="500px" />

## Getting Started

The Az.js CLI is built using [Node.js](https://nodejs.org/en/) and distributed via NPM, and therefore, you'll need to have both installed on your development machine before you can get started. Additionally, Az.js (currently) requires Node.js v7.6.0 or greater (which you should be using anyways!), so if you have an older version installed, please update it before moving on.

> If you aren't already using a Node.js version manager, I would recommend using one, as they can help make it dramatically simpler to updgrade and toggle between Node.js versions. I prefer [NVS](http://github.com/jasongin/nvs) since it is cross platform (it runs on Windows, macOS and Linux), but there are other great ones available as well (e.g. [NVM](https://github.com/creationix/nvm)).

In order to install Az.js, simply run the following NPM command, which will make the `azjs` command globally available:

```shell
npm i -g azjs
```

Once that is done, simply CD into a directory that contains a Node.js app and run the following command:

```shell
azjs deploy
```

This command will walk you through the process of authentication with your Azure account, and will the provision the neccessary infrasturcutre, deploy your app, and begin streaming stdout toy our terminal.Additionally, it copies the URL of the deployed app to your clipboard, so feel free to open a browser, paste in the URL and browse your app!

<img width="643" alt="screen shot 2017-03-29 at 11 24 36 pm" src="https://cloud.githubusercontent.com/assets/116461/24490319/f76485e0-14d6-11e7-9e04-852af665d49a.png">

Once you've authenticated once, all subsequent commands will re-use that "session", so that you don't need to continue logging in. If you'd like to explicitly set the credentials that `azjs` uses, see the [authentication reference](#authentication-reference) below.

When you're done, you can confidently remove all of your Azure resources (to prevent incurring any unexpected charges) by CDing into the app directory again and running the following command:

```shell
azjs remove
```

This will delete all of the infrastructure that was originally provisioned by running `azjs`.

## CLI Command Reference

* [Browse](#azjs-browse)
* [Deploy](#azjs-deploy)
* [Logs](#azjs-logs)
* [Remove](#azjs-remove)

### azjs browse

While running `azjs` will copy your app's URL to the clipboard, if you'd like to automatically have it launch a your default browser, and navigate to your app's deployed URL, you can simply run the following command at any time:

```shell
azjs browse
```

### azjs deploy

When you want to deploy a web app and/or push changes to an existing web app, you can quickly do this by CDing into your app directory and running the following command:

```shell
azjs deploy
```

This will zip up the contents of your web app and deploy them to Azure. It will then install your NPM dependencies, and begin displaying the log stream from your app's stdout.

#### Local Git Deployment

If you'd like to track your app via a local Git repo, and push changes to your web app as if it was another remote, then simply run the following command:

```shell
azjs deploy --git
```

This will behave exactly like the non-Git workflow, except it will also add a new Git remote to your repo called `azure` and then run a `git push` for you. If you'd like to suppress the automatic `git push`, simply pass the `--no-sync` flag, and do the push to the added `azure` remote as appropriate.

As you make changes to your app, you can simply run `git push azure master` in order to deploy them, as opposed to needing to run `azjs deploy` again.

#### Remote Git Deployment

If you're already tracking your app via a remote Git repo (e.g. GitHub), you can "connect" your web app to that by running the following command:

```shell
azjs deploy --git-url <GIT_REPO_URL>
```

This will behave similarly to the local Git deployment solution, however, instead of pushing changes directly to your web app, you would push changes to the specified Git repo, and allow the deployed web app to pull updates from it.

### azjs logs

While running `azjs` will automatically begin streaming your app's stdout after deployment, if you'd like to re-open the log stream at any other time (e.g. you'd like to view the logs for an already deployed version of your app), you can simply run the following command:

```shell
azjs logs
```

### azjs remove

When you no longer need an app deployed in Azure, you can quickly delete all of its backing resources by running the following command:

```shell
azjs remove
```

## Authentication Reference

As mentioned in the "getting started" section, the `azjs` CLI defaults to using an interactive login experience upon first use. However, if you'd like to customize the credentials that `azjs` uses to manage your Azure account, you can set the following four environment variables (each have two variant names):

* **azureSubId / ARM_SUBSRIPTION_ID** - The ID of the Azure subscription that you'd like to manage resources within
* **azureServicePrincipalClientId / ARM_CLIENT_ID** - The name of the service principal
* **azureServicePrincipalPassword / ARM_CLIENT_SECRET** - The password of the service principal
* **azureServicePrincipalTenantId / ARM_TENANT_ID** - The ID of the tenant that the service principal was created in

> These are the same environment variables that are expected from the [Serverless](https://serverless.com/framework/docs/providers/azure/guide/credentials/) experience for Azure Functions. However, other tools such as Terraform use different names for these variables, so I'll be updating them to support both naming conventions.