# Final Steps to Production

The last chapters will show you how to deploy your application to production. For this, we use the free hosting service Heroku. On the way to deploying the application, we will uncover more about *create-react-app*.

## Eject

The following knowledge is not necessary to deploy your application to production, but it still bears mentioning. *create-react-app* comes with one feature to keep it extendable, but also to prevent a vendor lock-in. A vendor lock-in usually happens when you buy into a technology but there is no escape hatch from using it in the future. Fortunately, in *create-react-app* you have such an escape hatch with "eject".

In your *package.json* you will find the scripts to *start*, *test*, and *build* your application. The last script is *eject*. However, it is important you know that **it is a one-way operation.** That means **once you eject, you can't go back!** It is advisable to stay in the safe environment of *create-react-app* if you have just started creating applications in React.

If you feel comfortable enough to run `npm run eject`, the command copies all the configuration and dependencies to your *package.json* and a new *config/* folder. It converts the whole project into a custom setup with tooling that includes Babel and Webpack, and grants full control over all these tools.

Its official documentation says *create-react-app* is suitable for small to mid size projects, so you shouldn't feel obligated to use the "eject" command until you're ready.

### Exercises:

* Read about [eject](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject)

## Deploy your App

No application should stay on localhost; eventually, it has to go live to see how it will perform in real-life scenarios. Heroku is a platform as a service where you can host your application, and it offers seamless integration with React. Specifically, it's possible to deploy a *create-react-app* in minutes, with a a zero-configuration deployment which follows the philosophy of *create-react-app*.

There are two requirements before an application can be deployed to Heroku:

* Install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)
* Create a [free Heroku account](https://www.heroku.com/)

If you have installed Homebrew, you can install the Heroku CLI from command line:

{title="Command Line",lang="text"}
~~~~~~~~
brew update
brew install heroku-toolbelt
~~~~~~~~

Now you can use git and Heroku CLI to deploy your application:

{title="Command Line",lang="text"}
~~~~~~~~
git init
heroku create -b https://github.com/mars/create-react-app-buildpack.git
git add .
git commit -m "react-create-app on Heroku"
git push heroku master
heroku open
~~~~~~~~

That's all there is to it. If you run into problems, check these resources for troubleshooting options:

* [Git and GitHub Essentials](https://www.robinwieruch.de/git-essential-commands/)
* [Deploying React with Zero Configuration](https://blog.heroku.com/deploying-react-with-zero-configuration)
* [Heroku Buildpack for create-react-app](https://github.com/mars/create-react-app-buildpack)
