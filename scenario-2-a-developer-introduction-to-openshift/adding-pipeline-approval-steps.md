# Adding Pipeline Approval Steps

## 1. Edit the pipeline

Ordinarily your pipeline definition would be checked into a source code management system like Git, and to change the pipeline you'd edit the Jenkinsfile in the source base. For this workshop we'll just edit it directly to add the necessary changes. You can edit it with the oc command but we'll use the Web Console.

Open the monolith-pipeline configuration page in the Web Console \(you can navigate to it from **Builds → Pipelines** but here's a quick link\):

* Pipeline Config page at

[https://master.\[EVENT-NAME\].openshiftworkshop.com/console/project/\[your-username\]-coolstore-prod/browse/pipelines/monolith-pipeline?tab=configuration](https://master.[EVENT-NAME].openshiftworkshop.com/console/project/[your-username]-coolstore-prod/browse/pipelines/monolith-pipeline?tab=configuration)

On this page you can see the pipeline definition. Click **Actions → Edit** to edit the pipeline:

![Prod](https://lh4.googleusercontent.com/Osv5vmmxSKF-0PJFYKfkcM5oOq4q5VEU_PMBmrmqrYQFEGshoFGNB7tgres4S5tifV_0xcA8GPaa1ALA7M6o82_32ZlEFg40d_MS5agTEKcbiMq4OTDMvtZHjFH7J_T3r_jnIVv3)

In the pipeline definition editor, add a new stage to the pipeline, just before the Deploy to PROD step:

You will need to copy and paste the below code into the right place as shown in the below image.

```yaml
stage 'Approve Go Live'
  timeout(time:30, unit:'MINUTES') {
    input message:'Go Live in Production (switch to new version)?'
  }
```

Your final pipeline should look like:

![Prod](https://lh4.googleusercontent.com/gG28eg-X7ufNirWzgH9Yirke_08Vp1aEWZTvliEHiQxg_M-zynKwC-NTjZ0hA6A7Or_EJJnwjtnZ4ZqMH3DSzvUHD1nSMrmDM1hAERdF27dPcMAZxlwtnC_2bZ9AjwPpXxvxUQdj)

Click **Save.**

## 2. Make a simple change to the app

With the approval step in place, let's simulate a new change from a developer who wants to change the color of the header in the coolstore back to the original \(black\) color.

As a developer you can easily un-do edits you made earlier to the CSS file using the source control management system \(Git\). To revert your changes, execute:

```bash
git checkout src/main/webapp/app/css/coolstore.css
```

Next, re-build the app once more:

```bash
mvn clean package -Popenshift
```

And re-deploy it to the dev environment using a binary build just as we did before:

```bash
oc start-build -n [your-username]-coolstore-dev coolstore --from-file=${HOME}/projects/monolith/deployments/ROOT.war
```

Now wait for it to complete the deployment:

```bash
oc -n [your-username]-coolstore-dev rollout status -w dc/coolstore
```

And verify that the original black header is visible in the dev application:

* Coolstore - Dev at

[http://www-\[your-username\]-coolstore-dev.apps.\[EVENT-NAME\].openshiftworkshop.com](http://www-[your-username]-coolstore-dev.apps.[EVENT-NAME].openshiftworkshop.com)

![Prod](https://lh6.googleusercontent.com/oDpdEQLygkM2-UDHk-Zo8drh0_vulzpAsUdS6MOQ_5RldOdMql8ZK8siguXwEgAxtfeUYtw-OdRki3eds-bo9ZCKFYTzyP7X-v0Cqr-gGSHDjXEBQ2bwiup_Iclw2kItNDrABBmA)

While the production application is still blue:

* Coolstore - Prod at

[http://www-\[your-username\]-coolstore-prod.apps.\[EVENT-NAME\].openshiftworkshop.com](http://www-[your-username]-coolstore-prod.apps.[EVENT-NAME].openshiftworkshop.com)

![Prod](https://lh5.googleusercontent.com/2awgQALiJtiJj5LpaYmKH8GZpSdN75VSwooTvVrkdaFcuYp5W5tZWzlcwWJimmqH2r4OUktMdR4SmpLlJQ6IELzvWGGqMWMZPzNH0goY_LedACB7JtbG1WpXuaU1xKgx8gbCKX3k)

We're happy with this change in dev, so let's promote the new change to prod, using the new approval step!

## 3. Run the pipeline again

Invoke the pipeline once more by clicking Start Pipeline on the Pipeline Config page at

[https://master.\[EVENT-NAME\].openshiftworkshop.com/console/project/\[your-username\]-coolstore-prod/browse/pipelines/monolith-pipeline](https://master.[EVENT-NAME].openshiftworkshop.com/console/project/[your-username]-coolstore-prod/browse/pipelines/monolith-pipeline)

The same pipeline progress will be shown, however before deploying to prod, you will see a prompt in the pipeline:

![Prod](https://lh6.googleusercontent.com/XxtACo8u8cKjpaW6WHtwtH1s3GgJZ-eB9cu4b1pycVw8-6eB-xVfms-GrqOm1Qm10YvngemLxBJ85W-cYgozV7cQ1VOvlvxOifJhon6mstJ1txrtA8YhobcqR7lqOXYlN7t9Mebd)

Click on the link for Input Required. This will open a new tab and direct you to Jenkins itself, where you can login with the same credentials as OpenShift:

* **Username:** userN
* **Password:** openshift

Accept the browser certificate warning and the Jenkins/OpenShift permissions, and then you'll find yourself at the approval prompt:

![Prod](https://lh5.googleusercontent.com/WcPJZvQ8ZSrqUfzaKtiu7RgODjKYYFPiYDEjLTDk6Nf4nb0O6YU7_3olELH-aTW0cskUgrfYKCctbZ8kKbrMHmHTMh-5KXIbRkX0CMhctPSJJ0bpstd-54G1y3ItvBeYIEkeAv5U)

## 4. Approve the change to go live

Click Proceed, which will approve the change to be pushed to production. You could also have clicked Abort which would stop the pipeline immediately in case the change was unwanted or unapproved.

Once you click Proceed, you will see the log file from Jenkins showing the final progress and deployment.

Wait for the production deployment to complete:

```text
oc rollout -n [your-username]-coolstore-prod status dc/[your-username]-coolstore-prod
```

Once it completes, verify that the production application has the new change \(original black header\):

* Coolstore - Prod at

[http://www-\[your-username\]-coolstore-prod.apps.\[EVENT-NAME\].openshiftworkshop.com](http://www-[your-username]-coolstore-prod.apps.[EVENT-NAME].openshiftworkshop.com)

![Prod](https://lh4.googleusercontent.com/W-OKqngxAUNVv-5bqWx78vHiNi3O7xfV1q7t4mvvGIseLbRkODb0TjkaR11ZuFqUdYyiEqeHQDydrg5ti_pyTmKyrwnYbQ2OOAz6SLsGUFikTsG-j9NuGx2hoGVJ1jHKH9lDA67S)

