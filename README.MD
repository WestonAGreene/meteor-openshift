## How to use

This is not to install Meteor on OpenShift, but a work around to convert your Meteor app into a node.js app then push the app to a node.js cartridge on OpenShift.
Two directories will be used:
 1) meteorApp (the directory from which your app is run locally)
 2) openshiftBuild (the directory into which you will convert your meteor app into a node.js app and then push to OpenShift)

First, create an openshift app with nodejs-0.10 and MongoDB 3.x custom cartridge.
With terminal open in the future parent directory of your openshiftBuild directory:
```bash
rhc app create openshiftBuild-appname \
  nodejs-0.10 \
  https://raw.githubusercontent.com/icflorescu/openshift-cartridge-mongodb/master/metadata/manifest.yml
```

`rhc` will automatically clone your new app into a directory. That directory is your openshiftBuild directory.
At this point, you don't need anything of the default content that is shipped by nodejs cartridge in your openshiftBuild directory.
Remove it all.
```bash
cd openshiftBuild-appname
git rm -rf * .openshift
```

Now, We have to fetch the meteor bundler from this git. You can use the below git commands, but commonly access is denied. Instead download the .zip of the files at this github and drag the contents into your openshiftBuild directory: github.com/jeeeyul/meteor-openshift.git

```bash
git remote add meteor-openshift -m master git@github.com:jeeeyul/meteor-openshift.git
git pull -s recursive -X theirs meteor-openshift master
```
Now, time to build your meteor app from your meteorApp directory.

```bash
cd meteorApp/
meteor build openshiftBuild/ --directory
```
It will generate a `bundle` directory into your openshiftBuild directory.


Then, push your directory:
```bash
cd openshiftBuild
git add --all && git commit -m "First Launch" && git push
```

That's all.


## How to update your meteor app

Just replace the `bundle` directory with a new build, then push it.
I have my openshiftBuild directory and my meteorApp directory within the same parent directory which allows me to 1-line update while my terminal instance is within my meteorApp directory:
```bash
meteor build ../openshiftBuild/ --directory && (cd ../openshiftBuild/ && git add -A &&  git commit -m ‘commitMSG’ && git push)
```

## Enable hot deploy

If you successfully deploy your app first time, Enabling hot deploy is recommanded.

```bash
cd path/to/your/openshift-appname
touch .openshift/markers/hot_deploy
git add --all && git commit -m "First Launch" && git push
```

