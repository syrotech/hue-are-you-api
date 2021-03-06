[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# Rails Deployment with Heroku

Ready to deploy your Rails app to Heroku? Follow the guide below and you will have a hosted Rails app that you can share with the world!

## Prerequisites

- [rails-api](https://git.generalassemb.ly/ga-wdi-boston/rails-api)
- This guide assumes you have followed the installation instructions for the [rails-api-template](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template#installation)
  up to where the steps mention Heroku.

## Getting Set Up

Before you can begin deploying your applications to Heroku, there are some
things you'll need to do first.

1. [Create a GitHub repository for your project](https://help.github.com/articles/create-a-repo/).
1. [Create a Heroku account](https://www.heroku.com).
    You will be sent an activation email, so be sure to check your inbox so that
    you can activate your account.
1. Install the Heroku Command Line Tools:
   - On macOS, run `brew install heroku/brew/heroku`.
   - On Ubuntu/WSL, run `curl https://cli-assets.heroku.com/install.sh | sh`.
1. **Login to Heroku** by running `heroku auth:login` from the console and providing
    your Heroku credentials when asked. Once you log in, if you're prompted
    to add these credentials to your keychain, say yes. *You will not be able*
    *to see your password as you type*.

## Deploying to Heroku: Checklist

Now you're set up to use Heroku.

To deploy a new application to Heroku:

- [ ] [Create a New Heroku App](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template/blob/master/rails-heroku-deployment-guide.md#create-a-new-heroku-app)
- [ ] [Push `master` to Heroku](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template/blob/master/rails-heroku-deployment-guide.md#push-master-to-heroku)
- [ ] [Update Heroku's Database](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template/blob/master/rails-heroku-deployment-guide.md#update-herokus-database)
- [ ] [Set Your Secrets](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template/blob/master/rails-heroku-deployment-guide.md#setup-your-production-environment)
- [ ] [Check Your Work](https://git.generalassemb.ly/ga-wdi-boston/rails-api-template/blob/master/rails-heroku-deployment-guide.md#check-your-work)

Next, we will take a look at each of these steps in detail.

### Create a New Heroku App

Go to the root of your repo and run `heroku create`. This will create an awesome
_autogenerated_ name for your app, and add a new remote repository to your repo
 called _heroku_. View your remotes by typing `git remote -v`. You should see
something like:

```sh
heroku  git@heroku.com:agile-badlands-7658.git (fetch)
heroku  git@heroku.com:agile-badlands-7658.git (push)
origin  git@github.com:devloper/sei_project_2_backend.git (fetch)
origin  git@github.com: devloper/sei_project_2_backend.git (push)
```

### Push `master` to Heroku

Heroku is really smart. When you `git push` to your Heroku app's `master` branch, it automagically updates your running application to have your new code. This is called using a deploy hook.

Only keep clean, working code on `master`. After you complete a feature, merge
it onto `master`. Push your updated `master` to Heroku, and the deploy will follow.

```sh
git checkout master
git merge my-feature # merge your working code
git push origin master # update GitHub
git push heroku master # update Heroku
```

### Update Heroku's Database
  
Once you've deployed your code, you can safely run new migrations. You'll need
to do this step every time you have new migrations.

```sh
heroku run rails db:migrate
```

If you have seeds or examples, or if you've updated seeds or examples, you
should also run them on Heroku.

```sh
heroku run rails db:seed
heroku run rails db:examples
```

### Setup your Production Environment

Your production environment needs to know certain things about your application that are not stored in your application code. We can configure _environment variables_ directly on our server to accomplish this. 

In your Heroku app, we will be using the `heroku config` command. From the root of your local application in the console, run the following:

1. Configure CORS for your production app by setting the `CLIENT_ORIGIN` variable and point it to your client's root URL:
   - `heroku config:set CLIENT_ORIGIN=https://<github-username>.github.io`


1. Configure application secret key, depending on Rails version.
	- _Rails Versions >= 5.2_: Configure the `RAILS_MASTER_KEY` variable and set it to the same `config/master.key` file you have locally (this was automatically generated when you ran `bin/rails credentials:edit`):
   		- `heroku config:set RAILS_MASTER_KEY="$(< config/master.key)"`
   
		- Check your config variables at any time by typing `heroku config`. Make sure you have a `RAILS_MASTER_KEY` and a `CLIENT_ORIGIN`.

	- _Rails Versions < 5.2_: Configure `SECRET_KEY_BASE` variable:
		- `heroku config:set SECRET_KEY_BASE=$(rake secret)`
		- You can verify you set this correctly with `heroku config:get SECRET_KEY_BASE` 

- Note: You can also edit your app's config from the Heroku website. It's worth having a look at your app's dashboard on the Heroku website.

### Check Your Work

Ready to see your live application? Run:

```sh
heroku open
```

You'll probably see something like this:

![](https://cloud.githubusercontent.com/assets/388761/13259005/93c9fdf6-da23-11e5-9c90-19c59580944a.png)

That's normal, **unless** you have defined a root route. Try going to `/examples` to get some actual output (even if it's an empty array).

Heroku will automatically run your server and update your live application when you push updates to it or change config variables.

   - If you need to restart your server at any time, run `heroku restart`
   - If you need to view live server logs, run `heroku logs --tail`. Quit with `ctrl-c`

---

### Change Your App's Name (optional)

If you wish you can rename your app at any time. It must be unique across all
apps deployed to Heroku.

```sh
heroku apps:rename newname
```

Your app will become immediately available at it's new subdomain,
`newname.herokuapp.com`.

Don't forget to update any clients with your new API app name.

---

## Heroku Command Reference

A full list of Heroku commands can be accessed by running `heroku --help`; below
are some of the more common ones.

|                Commands                |                                                 Behavior                                                 |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------- |
|             `heroku logs [--tail]`              |                                   Running just `heroku logs` will show you the server logs from your deployed API. The `--tail` flag is optional.                        |
|            `heroku run ...`            |                                    Run a program from within Heroku. Examples (`heroku run rails console`, `heroku run rails db:migrate`).                                     |
|            `heroku config`             |                           Environmental variables in your current Heroku app.                            |
|            `heroku config:set CLIENT_ORIGIN=https://yourgithubname.github.io`            |                                    Set CLIENT_ORIGIN.                                     |
|            `heroku apps:rename newname`            |                                    Rename Heroku app name (entirely optional).                                     |
|            `heroku restart`            |                                    Restart the Heroku app, make sure you do this after changing your API.                                     |
|            `heroku open`            |                                    Open your Heroku app in default browser.                                     |
|            `heroku --help`            |                                    Displays a Heroku CLI usage summary.                                     |

## WARNING: Ephemeral Filesystem

One serious limitation of Heroku is that it provides an "ephemeral filesystem";
in other words, if you try to save a new file inside the repo (e.g. an uploaded
image file), it will disappear when your app is restarted or redeployed.

As an example, try running the following commands:

```sh
heroku run bash
touch happy.txt; echo 'is happy' > happy.txt
cat happy.txt
```

Then, hit Ctrl-D to get out of Heroku bash shell. If you re-open the shell and
run `ls -l`, `happy.txt` will be missing!

The typical workaround is to save files in cloud storage such as [Amazon
S3](https://aws.amazon.com/s3/); more on this in the near future.

![](https://www.thehinzadventures.com/wp-content/uploads/2015/03/54843046.jpg)

## Troubleshooting

If your Heroku deployment isn't working as expected, review these steps above carefully.

Below are some common scenarios for broken apps and how to get them up and running again:

-  You can't fix what you can't see. View your production server logs with `heroku logs --tail`

- Sometimes you just need to kick over your running application. Restart it with `heroku restart`, and watch your server logs for errors as you access resources.
- Make sure you are looking at the correct app: `heroku open`
- Make sure you pushed your _master_ branch to Heroku's _master_ branch (and you didn't try to push a _feature_ branch instead)
- Double check your config vars using `heroku config`. Look for mismatched or missing values.

- Remember, **the production database is entirely
separate from your development database**. The DB is a very common source of application errors. Double check you have run all pending migrations with `heroku run db:migrate`

- Sometimes processes take too long and _time out_, meaning after 30 seconds in Heroku's case, the server simply blocks the process from continuing. How do you bypass this? One way to deal with long-running processes is to detach it: `heroku run:detached rake db:migrate` (more info [at the Heroku devcenter](https://devcenter.heroku.com/articles/one-off-dynos#running-tasks-in-background))

- You can always use the `psql` console, even on Heroku. Run `heroku pg:psql` to connect to the production database with the PSQL
client. Be cautious though, it's possible to accidentally destroy production data from the CLI (and you can't recover data unless you have it backed up).

### Deploying an app _not_ based on rails-api-template?

Starting a rails project from scratch? Want to learn more about Heroku and Rails? A great resource for you to use is [Heroku's getting started guide](https://devcenter.heroku.com/articles/getting-started-with-rails6)

Heroku has the ability to host many other kinds of apps beyond just Rails. Use your favorite search engine to learn more!

## Additional Resources

- [Heroku Command Line](https://devcenter.heroku.com/categories/command-line)
- [Heroku Rails Deployment](https://devcenter.heroku.com/articles/getting-started-with-rails6)

## [License](LICENSE)

1. All content is licensed under a CC??BY??NC??SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
    alternative licensing, please contact legal@ga.co.