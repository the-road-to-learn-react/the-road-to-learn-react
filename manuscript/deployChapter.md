# Deploy your App to Production

In the end no app should stay on localhost. You want to go live! Heroku is a platform as a service where you can host your app. They offer a seamless integration with React. To be more specific: It's possible to deploy a create-react-app in minutes. It's a zero-configuration deployment which follows the philosophy of create-react-app.

You need to fulfill two requirements before you can deploy your app to Heroku:

* install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-command-line)
* create a [free Heroku account](https://www.heroku.com/)

If you have installed Homebrew, you can install the Heroku CLI from command line:

{lang=javascript,linenos=off}
~~~~~~~~
brew update
brew install heroku-toolbelt
~~~~~~~~

Now you can use git and Heroku CLI to deploy your app.

{lang=javascript,linenos=off}
~~~~~~~~
git init
heroku create -b https://github.com/mars/create-react-app-buildpack.git
git add .
git commit -m "react-create-app on Heroku"
git push heroku master
heroku open
~~~~~~~~

That's it. I hope your app is up and running now. If you run into problems you can check the following resources:

* [Deploying React with Zero Configuration](https://blog.heroku.com/deploying-react-with-zero-configuration)
* [Heroku Buildpack for create-react-app](https://github.com/mars/create-react-app-buildpack)
