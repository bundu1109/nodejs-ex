Run Meteor 1.2 on Red Hat's OpenShift PaaS
====================================================================
This git repository is a sample Meteor base cartidge with can be used with your
Meteor application to run it on openshift.com PaaS platform

The catridge would install NodeJS version 0.10.40

Step 0
----------------------------------------------------------
1. Create on Openshift account at http://openshift.com/
2. Install rhc tools to manage your openshift account :
        https://developers.openshift.com/en/managing-client-tools.html


Step 1 - Get NodeJS v0.10.40 running on your openshift app
----------------------------------------------------------

Create a new application `mynodeapp` in your openshift login control panel
using the standard NodeJS 0.10 cartrige.
You may choose to add auto-scaling while setting this up.
Choosing autoscaling would add an HAProxy instance you your application.

Now clone the git URL available on your application page to your local system

    git clone SOURCE_CODE_URL  mylocalnodeapp

Add this `github meteor-openshift` repository to mylocalnodeapp

    cd mylocalnodeapp
    git remote add upstream -m master git://github.com/sachinbhutani/meteor-openshift.git
    git pull -s recursive -X theirs upstream master
	
Check if the execution flag is set for scripts located inside the given folders:

    .openshift\action_hooks 
	.openshift\lib

Then commit and push the repo to OpenShift

    git add --all
    git commit -a -m 'nodejs'
    git push

You should now have a NodeJs version 0.10.40  application running at:

    http://mynodeapp-mydomain.rhcloud.com
    ( See env @ http://mynodeapp-mydomain.rhcloud.com/env )

Check the URL http://mynodeapp-mydomain.rhcloud.com/env and save the environment variables to a local file.
You may need some of them if something goes wrong.

Step 2 Setup Mongo DB 3.0
------------------------------------------------------------
On your application page go to the link "See the list of cartridges you can add", paste the URL below in "Install your own cartridge" textbox at the bottom of the page and click "Next".

        http://cartreflect-claytondev.rhcloud.com/github/icflorescu/openshift-cartridge-mongodb

Alternatively if you like to use another MongoDB database hosts like http://compose.io or http://mongolab.com/
You would need to update the `meteorshim.js` file as described in the next section with a MONGO_URL if you plan to use an external MongoDB host.

Step 3 Update env variables
------------------------------------------------------------
Update the file `meteorshim.js` to use your own MONGO_URL env variable.
MAIL_URL and other env variables may also be added as needed.

Step 4 Adding your Meteor App
------------------------------------------------------------
Build your meteor app and un-bundle it into the `mylocalnodeapp` directory for pushing to OpenShift

The following steps need to be repeated each time you want to push an updated version of your meteor app to openshift.
You may create a shell script for it in your meteor repo

Assuming your awesome meteor app repo is in dir `mymeteorapp`

    cd mymeteorapp
    meteor build tarball
    cp tarball/mymeteorapp.tar.gz ~/path/to/mylocalnodeapp
    rm tarball/mymeteorapp.tar.gz
    cd ~/path/to/mylocalnodeapp

    # for Mac or BSD-based
    tar -xvf mymeteorapp.tar.gz -s '/^bundle//'

    # for Linux, or using GNU tar
    tar -xvf mymeteorapp.tar.gz --transform 's|^bundle/||'

    rm mymeteorapp.tar.gz
    git add --all
    git commit -a -m 'meteor-openshift'
    git push

After a lot of messages from the remote server, your awesome Meteor app should be finally running at
    http://mynodeapp-mydomain.rhcloud.com

----------------------------------------------------------------------------------------
This repo has not been throughly tested, please report any issues or suggest improvements.
