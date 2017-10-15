# Final Steps to Production

The last chapters will show you how to deploy your application to production. You will use the free hosting service Heroku. On the way to deploy your application, you will learn more about *create-react-app*.

## Eject

The following step and knowledge is **not necessary** to deploy your application to production. Still, I want to explain it to you. *create-react-app* comes with one feature to keep it extendable but also to prevent a vendor lock-in. A vendor lock-in usually happens when you buy into a technology but there is no escape hatch of using it in the future. Fortunately in *create-react-app* you have such an escape hatch with "eject".

In your *package.json* you will find the scripts to *start*, *test* and *build* your application. The last script is *eject*. You could try it, but there is no way back. **It is a one-way operation. Once you eject, you can't go back!** If you just started to learn React, it makes no sense to leave the convenient environment of *create-react-app*.

If you would run `npm run eject`, the command would copy all the configuration and dependencies to your *package.json* and a new *config/* folder. You would convert the whole project into a custom setup with tooling that includes Babel and Webpack. After all, you would have full control over all these tools.

The official documentation says that *create-react-app* is suitable for small to mid size projects. You shouldn't feel obligated to use the "eject" command.

### Exercises:

* read more about [eject](https://github.com/facebookincubator/create-react-app#converting-to-a-custom-setup)

## Deploy your App

In the end, no application should stay on localhost. You want to go live. Heroku is a platform as a service where you can host your application. They offer a seamless integration with React. To be more specific: It's possible to deploy a *create-react-app* in minutes. It is a zero-configuration deployment which follows the philosophy of *create-react-app*.

You need to fulfill two requirements before you can deploy your application to Heroku:

* install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-command-line)
* create a [free Heroku account](https://www.heroku.com/)

If you have installed Homebrew, you can install the Heroku CLI from command line:

{title="Command Line",lang="text"}
~~~~~~~~
brew update
brew install heroku-toolbelt
~~~~~~~~

Now you can use git and Heroku CLI to deploy your application.

{title="Command Line",lang="text"}
~~~~~~~~
git init
heroku create -b https://github.com/mars/create-react-app-buildpack.git
git add .
git commit -m "react-create-app on Heroku"
git push heroku master
heroku open
~~~~~~~~

That's it. I hope your application is up and running now. If you run into problems you can check the following resources:

* [Git and GitHub Essentials](https://www.robinwieruch.de/git-essential-commands/)
* [Deploying React with Zero Configuration](https://blog.heroku.com/deploying-react-with-zero-configuration)
* [Heroku Buildpack for create-react-app](https://github.com/mars/create-react-app-buildpack)
