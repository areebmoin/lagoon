# First Deployment of Drupal

It's time! It's time for the first deployment into Lagoon! We hope you are as excited as we are!

![excited](https://i.giphy.com/media/7kVRZwYRwF1ok/giphy-downsized.gif)

## 1. Make sure you are all set

In order to make your first deployment a successful one, please make sure that your [Drupal Project is Lagoonized](./lagoonize.md) and you have setup the project in Lagoon. If not, don't worry, just follow the [Step-by-Step Guides](../index.md) which show you how this works.

## 2. Push!

With Lagoon you create a new deployment with just pushing into a branch that is configured to be deployed.

If you don't have any new code to push, don't worry, just run

    git commit --allow-empty -m "go, go! Power Rangers!"
    git push

This will trigger a push and the Git hosting should inform Lagoon about this push via the configured webhook.

If all is correct you should see a notification in your configured chat system (this is configured by your friendly Lagoon Administrator):

![Deployment in Slack Start](/images/first_deployment_slack_start.jpg)

This tells your that Lagoon just started to deploy your code. Depending on the size of the code and amount of containers this will take a couple of seconds. Just relax. If you'd like to know whats happening now, check out the [Deployment & Build Process of Lagoon](../build_deploy_process.md)

You can also check your Lagoon UI to see the progress of any deployment (your Lagoon administrator has the info).

## 4. A fail

Depending of your post-rollout tasks inside `.lagoon.yml` you might have run some tasks like `drush updb` or `drush cr`. These Drush tasks depend on a database existing within the environment, which obviously does not exist yet. Let's fix that:

## 5. Synchronize local database to the remote Lagoon environment

With full Drush site alias support in Lagoon it is super easy to synchronize a local database with the remote lagoon environment.

First let's make sure that you can see the Drush Site Aliases:

    drush sa

This should return your just deployed environment (let's assume you just pushed into `develop`):

    [drupal-example]cli-drupal:/app$ drush sa
    @develop
    @self
    default

!!! hint
    If you get an error like `Permission denied (publickey)`, check out the documentation here: [pygmy - adding ssh keys](https://pygmy.readthedocs.io/en/master/usage/#adding-ssh-keys)

With this we can now synchronize the local database (which is represented in drush via the site alias `@self`) with the remote one (`@develop`):

    drush sql-sync @self @develop

You should see something like:

    [drupal-example]cli-drupal:/app$ drush sql-sync @self @develop
    You will destroy data in ssh.lagoon.amazeeio.cloud/drupal and replace with data from drupal.
    Do you really want to continue? (y/n): y
    Starting to dump database on Source.                                                                              [ok]
    Database dump saved to /home/drush-backups/drupal/20180227075813/drupal_20180227_075815.sql.gz               [success]
    Starting to discover temporary files directory on Destination.                                                    [ok]
    You will delete files in drupal-example-develop@ssh.lagoon.amazeeio.cloud:/tmp/drupal_20180227_075815.sql.gz and replace with data from /home/drush-backups/drupal/20180227075813/drupal_20180227_075815.sql.gz
    Do you really want to continue? (y/n): y
    Copying dump file from Source to Destination.                                                                     [ok]
    Starting to import dump file onto Destination database.

Now let's try another deployment, again just an empty push:

    git commit --allow-empty -m "go, go! Power Rangers!"
    git push

This time all should be green:

![Deployment in Slack Success](/images/first_deployment_slack_2nd_success.jpg)

Click on the links and you should see your Drupal site loaded in all its beauty, just most probably without iamge, which we will handle in Step 6.

If it is still failing, check the `Logs` link for more information.

## 6. Synchronize local files to the remote Lagoon environment

You probably guessed it: we can do it with Drush:

    drush rsync @self:%files @develop:%files

It should show you something like:

    [drupal-example]cli-drupal:/app$ drush rsync @self:%files @develop:%files
    You will delete files in drupal-example-develop@ssh.lagoon.amazeeio.cloud:/app/web/sites/default/files and replace with data from /app/web/sites/default/files/
    Do you really want to continue? (y/n): y

In some cases, though, it might not look correct, like here:

    [drupal-example]cli-drupal:/app$ drush rsync @self:%files @develop:%files
    You will delete files in drupal-example-develop@ssh.lagoon.amazeeio.cloud:'/app/web/%files' and replace with data from '/app/web/%files'/
    Do you really want to continue? (y/n):

The reason for that is, that the Drupal cannot resolve the path of the files directory. This most probably has to do that the Drupal is not fully configured or has a missing database. For an easy workaround you can use `drush rsync @self:sites/default/files @develop:sites/default/files` but we suggest that you actually check your local and remote Drupal (you can test with `drush status` to see if the files directory is correctly configured).


## 7. It's done!

As soon as Lagoon is done building and deploying it will send a second notification to the chat system, like so:

![Deployment in Slack Success](/images/first_deployment_slack_success.jpg)

This tells you:

- Which project has been deployed
- Which branch and Git SHA has been deployed
- A link to the full logs of the build and deployment
- Links to all routes (URLs) where the environment can be reached.

That's it! We hope that wasn't too hard - making devops is accessible is what we are striving for.

## But wait, how about other branches or the production environment?

That's the beauty of Lagoon: it's exactly the same: Just push the branchname you defined to be your production branch and that one will be deployed.

## Failure? Don't worry.

Did the deployment fail? Oh no! But we're here to help:

1. Click on the `Logs` Link in the error notification, it will tell you where in the deployment process the failure happened.
2. If you can't figure it out, just ask your Lagoon Support, they are definitely here to help!
