# Promoting Apps Across Environments with Pipelines

As part of the production environment template you used in the last step, a Pipeline build object was created. Ordinarily the pipeline would contain steps to build the project in the _dev_ environment, store the resulting image in the local repository, run the image and execute tests against it, then wait for human approval to _promote_ the resulting image to other environments like test or production.

1. Inspect the Pipeline Definition

Our pipeline is somewhat simplified for the purposes of this Workshop. Inspect the contents of the pipeline using the following command:

```bash
oc projects
oc project [your-username]-coolstore-prod
oc describe bc/monolith-pipeline
```

You can see the Jenkinsfile definition of the pipeline in the output:

```text
Jenkinsfile contents:
  node ('maven') {
    stage 'Build'
    sleep 5

    stage 'Run Tests in DEV'
    sleep 10

    stage 'Deploy to PROD'
    openshiftTag(sourceStream: 'coolstore', sourceTag: 'latest', namespace: 'coolstore-dev', destinationStream: 'coolstore', destinationTag: 'prod', destinationNamespace: 'coolstore-prod')
    sleep 10

    stage 'Run Tests in PROD'
    sleep 30
  }
```

Note that source namespace refers to coolstore-dev and destinationNamespace to coolstore-prod. We will need to add \[your-username\]- in the coolstore-dev and coolstore-prod project name to associate the pipeline to the project created in the previous lab. It will be done in the next steps.

Pipeline syntax allows creating complex deployment scenarios with the possibility of defining checkpoint for manual interaction and approval process using [the large set of steps and plugins that Jenkins provides](https://jenkins.io/doc/pipeline/steps/) in order to adapt the pipeline to the process used in your team. You can see a few examples of advanced pipelines in the [OpenShift GitHub Repository](https://github.com/openshift/origin/tree/master/examples/jenkins/pipeline).

To simplify the pipeline in this workshop, we simulate the build and tests and skip any need for human input. Once the pipeline completes, it deploys the app from the dev environment to our _production_ environment using the above `openshiftTag()` method, which simply re-tags the image you already created using a tag which will trigger deployment in the production environment.

2. Promote the dev image to production using the pipeline

You can use the oc command line to invoke the build pipeline, or the Web Console. Let's use the Web Console.

First of all, let's ensure `coolstore-prod` has the user groups needed to run the pipeline. Return to the main page by clicking in openshift logo on the left top corner.

![](https://lh6.googleusercontent.com/3eP6yMr8GZjOhIKIjH7zL8osBex_zR2Bmk4xXaIpmr2EyYNIogU-f4m53X1HbHmNAJNZKxTUX8MoQroKFnYXPc4_xtJFZyUwONOfevwtv4w_PFdNrf5iI0eFPpEERPWrAER9Tj7m)

Click in the ![](https://lh4.googleusercontent.com/PUD0N7kbW9hjzSLxGkLMmGdlO6smSM11TedsakaDcqgf6nwdKMQfQ5bCjr-xLPe4Dsvcc_zRtzjEsqJoqiDRKN1Npx5RTV89Sl3I7rOwVdKQSH0IFJmZ5bl5bgH-aHQdeauwkRk9)button next to the \[your-username\]-coolstore-dev project -&gt; View Membership.

![](https://lh6.googleusercontent.com/F9WuZOHwFU0UzzgeJzQCkmtTsw5eGBokYLjXBL0rQQSt4W6BF1Q5MkdAlGkB0HxxHbC9ViWyi6mjLfbhv0CWu60H0GfjwALBpVXKeXe_bS4LGkv39PokPn8F8ElQdaxacXHL8810)

Navigate to Service Groups, notice that **system:serviceaccounts:coolstore-prod** doesn't exist. The correct name is `coolstore-prod` instead, so the pipeline won't run successfully, it can't get the image streams in the `-dev` namespace. You can do the same steps in `coolstore-prod` project to check the service accounts created to its project.

![](https://lh4.googleusercontent.com/WUeY_7oPGQxoOFvHCWuQT78keYLWBEqsfxchE2ci2rbsyeMkSHGsuK1t9gXbdNKjoNHPmYfMCoipl-YxgKN__YeFvyR1Rh0s9Bhd2HKVXnsTczyM572mZcR6t0XgCgw71FrraJgw)

The pipeline copies the ROOT.war running in developer to product environment. Without the correct service account in `coolstore-dev` project the image streams "coolstore" is forbidden and the pipeline created in `coolstore-prod` will thrown errors. So, click in Edit Membership button.

![](https://lh3.googleusercontent.com/OOPaXi4nLipkhWr80iSf_S-Svp7LJDQFGDNyYdvl17xuat_hHy5z4YTIIXOexa1BAdIjZrrpwGakjNxc508VVOn7bdBsFKKp3HpOViiIkSjMBVyyjRPIirmix9RCbgjkQUZuYP0d)

Add a new group called **system:serviceaccounts:coolstore-prod** with the admin role.

![](https://lh5.googleusercontent.com/zKNzl4Sj63yQGIqp5w6ObQpxODyLW_FCOM8jZSNR9DYfDz1g5Kb3LiaLJ_zJipj1JTX9S8eqcPTe3pYKPBJ13-lUbKWT2e8ZJHfcsoTrROuD93jsKSdqZ2WWzbsqA0TNWj00HQoz)

Remove the wrong role called **system.serviceaccounts:coolstore-prod**.

![](https://lh3.googleusercontent.com/RPG8dQR3Ua12f9pXhHXdu07vhPXKIWJDLjuHs_Wt29B96Om2fID1lIORAh0ahSDWZyWzZ-9AciBPtDryKRBnU09f8uW3tYK5VniB_wv-857wcgxbZWpFMZ34VS0smzuwy-exNSuY)

Click in Remove button to confirm it and Done Editing.

![](https://lh6.googleusercontent.com/O1rroWybf6oy-ZMRREIXW4gibQK7tD55kn9n7IPLjG9mEBxh-TW9HbIU5cwSh09y-jU1_3_OccxZ2d4j3F2QAlWFmEyBp3pFBWTYE08qjoKV0tQmHV_WCdiA7vJAJELlqmYT0GG0)

Now, we are going to fix the pipeline. Open the production project in the web console:

* Web Console - Coolstore Monolith Prod at

`https://master.[EVENT-NAME].openshiftworkshop.com/console/project/coolstore-prod`

Navigate to **Builds -&gt; Pipelines -&gt; monolith-pipeline**

Click in the monolith-pipeline.

![](https://lh6.googleusercontent.com/Y2rb14cIoJiP3DNwQ8NRCfBOrmZ-XUN_GZHn4SF6LW9hcP1VdENwt-t6zF-Vx1QqLHin80R3-N_OFjxUjdAolOa9pxRSXPJRAE0tFf6Inj8h4cSIbpJdgcFgn3lg1Fm37h9NQgG2)

Then, select **Actions -&gt; Edit**

![](https://lh5.googleusercontent.com/WriPBIh7dzZsprhAAHSNfNdu1w4kSDwIMG9gwlyMY6uBF4TUH_GW8CiSxZ7s9aA2nS00pVLde8EZVWjCZZE1K3a0-oG0__vxdvgYYOrdbACYDw2NYuy99j72EQKFCP9DazzAMO65)

And update the coolstore-dev namespace and coolstore-prod destinationNamespace adding \[your-username\].

![](https://lh6.googleusercontent.com/cCuB1vmwtomj5JBu0CD4LDcg2VSWANqBGtRRzaVG7xTGQ7ezeHQMe7CxOw40zgoSILn51r2YkmZW8uMO-63j3wVESfAsGjPun8HVSxoI2un53leTKiSIASs02hP3-kfBXXpZ789w)

The complete pipeline will be like this:

```text
Jenkinsfile contents:
  node ('maven') {
    stage 'Build'
    sleep 5

    stage 'Run Tests in DEV'
    sleep 10

    stage 'Deploy to PROD'
    openshiftTag(sourceStream: 'coolstore', sourceTag: 'latest', namespace: '[your-username]-coolstore-dev', destinationStream: 'coolstore', destinationTag: 'prod', destinationNamespace: '[your-username]-coolstore-prod')
    sleep 10

    stage 'Run Tests in PROD'
    sleep 30
  }
```

Save the pipeline by clicking in save button in the bottom of its page.

![](https://lh5.googleusercontent.com/HTzR2CgHSH_JO-mVQNzOh3R4mf37klTs9ZpgkXi3eeTzHej9tOnvKsz39qEh6MmtMZRJ0AG3BEcHwcttZBaJN8c4es8bqOynD2xP_DoaQWIVCruZfRBolof7JjVBY-bhHnJZnFqM)

Confirm Jenkins is running before performing the next steps. Click in Overview menu option.

![](https://lh4.googleusercontent.com/3yjMrc3hZUyor9xqK7HzDN5ybXg_a3fwx2SAcgHuhexTY6d9axYE7rqVXsXFASwxIbeNy6o7kYRPqOqmDKsjUN2droJ7iIP6v3K3GZHs0DqtEZZLZc1OwVePdrjyhEddiUbQG-S6)

Open Jenkins application details ![](https://lh3.googleusercontent.com/Lgk6Bb4NN0TvMLFNbiKjztJvKUQnt0YKdORcC1y24gzTI_dyhEvWE-7SVSYISM1zJuTmICZkzJficN1KUZAd7eYgXxoyWhKtpleVNWyRKn-W5F82WWmPrd9RtvKOBqdCGcxXTXkJ)

![](https://lh6.googleusercontent.com/J3tofb7pCMRxRRwH6fywHg3o_VxQU-4_PIbGvgQid0XIP8aqVQhw8vHn-fnX-UdoBQZ1BhbBikMDELwPU7Ut7c56amiPAmjgMI3ul99fD6RUtssQE8PlP_b3dDph3b91g_XJXF-y)

Check if it has a running status and completed with blue color

![](https://lh4.googleusercontent.com/8CPi6h2qELrkRsLlsQycKJFQ_UQ4fhevRnM606vzJIAtpaIcKx46jebMt-nnBCwi6BPXQ9h20YniUbf8CR7IJH8dxggZ5pgTKRQuiinqFOgnvg7cv7fAHVb7JyNCYGAOlb7orMc_)

Next, click return to the **Builds -&gt; Pipeline** menu and click Start Pipeline next to the `coolstore-monolith` pipeline:

![](https://lh4.googleusercontent.com/MocR2T5rd8z0yhhbijpPeEL2W7GUdMvg3GoagxlEn2YR-kWkbwy2VJCMPY7lWx6Ljn0lHij0mHara8UpSDhlQwZMGJzJmGs89Ps90B9kC0dftAicb_PNZUMu3ZlzlUK3JIjE4lvb)

This will start the pipeline. It will take a minute or two to start the pipeline \(future runs will not take as much time as the Jenkins infrastructure will already be warmed up\). You can watch the progress of the pipeline:

![Prod](https://lh3.googleusercontent.com/LpRMMrJSKF0n7bWU9NmFNoh_ePyCUhzMNzbg_5fO9si-yjc-rta2NULC3nOBayo14IT-XuHe4akihPA-yzb9TLc4okU6M9sGQoOUllcNBL8OdwCAVW9qOJHBxB71hK-2a6RgvLbq)

Once the pipeline completes, return to the Prod Project Overview at

`https://master.[EVENT-NAME].openshiftworkshop.com/console/project/coolstore-prod` and notice that the application is now deployed and running!

![Prod](https://lh5.googleusercontent.com/EEEO1UskQKm2hEojZ0nX7C4qQtQOukRYmqacQ7DPJ4ipDHvQrgAFMqjDvBU9yBwI65NQw0dHQPE5q6ndtZ5DFSty4rfdU6uCLlSkqTBjHbNTEnziXmxnOKvon1ZbNtR7RYVA50jJ)

View the production app with the blue header from before is running by clicking: CoolStore Production App at `http://www.coolstore-prod.apps.[EVENT-NAME].openshiftworkshop.com` \(it may take a few moments for the container to deploy fully.\)

