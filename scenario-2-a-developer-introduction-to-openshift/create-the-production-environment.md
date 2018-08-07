# Create the production environment

We will create and initialize the new production environment using another template in a separate OpenShift project.

1. Initialize production project environment

Execute the following oc command to create a new project:

```bash
oc new-project coolstore-prod --display-name='Coolstore Monolith - Production'
```

This will create a new OpenShift project called `coolstore-prod` from which our production application will run.

You can also create the project using JBoss Developer Studio or Openshift Web Console if you prefer.

Right click in the Openshift connection, go to **New -&gt; Project**

![](https://lh6.googleusercontent.com/zvYxZXGbfkwJZKzySEwAxhZFHUyeJO-af1e64lC1hswtv6vNNGEGxhm6qdOVOf1lNx95uSVMLgnV2ek6E5ZjWlic7INN2nQXGaAbi8vjhJLuAgwahid3KPnKTvVTNDJDfv-d0Wga)

Add the same project info in the fields

![](https://lh4.googleusercontent.com/FMcYVoJKWGLaxUtSyXO5StCeBJE-wYRHG5tdRzO0gLt5AcCIE24mwl51m33klJl6Ue46xy8rL5qGRmD2jxxsUxMxSZbduvwKA91WPeNaIyPxuW9_k_H6xb3gS9hyMACtGze_9qf6)

2. Add the production elements

In this case we'll use the production template to create the objects. Execute:

```bash
oc new-app --template=coolstore-monolith-pipeline-build
```

Or right click in the new Production **coolstore-prod -&gt; New -&gt; Application**

![](https://lh6.googleusercontent.com/iO3MZDEpjNXvxD3DbArXi_FPn81pU39lTZbG9-IjCK-OG5U0IzrgdMsg9eYCPJqztEfMQiSxHU1HXlc5_TdgLjS9oXY53mAR7SyP3h2ypmRuFwTNFFxk97QyWP8wbI4C-ZZtybIj)

Choose the monolith project and the coolstore-monolith-pipeline-build template.

![](https://lh4.googleusercontent.com/hTfft6zGe0IB18LCmidlICNJNj8tofCv2uLo1EoNeYUP1nExIrCiI3emyB8VuzgbQcv1c9CvwdPXxzQafgQkW2SjdBAFMLUURFEO8hzQRzXkcl_b9DhwjB_UsOC-s5EQynNF_C4Q)

Follow the wizard until the end.

This will use an OpenShift Template called `coolstore-monolith-pipeline-build` to construct the production application. As you probably guessed it will also include a Jenkins Pipeline to control the production application \(more on this later!\)

Navigate to the Web Console to see your new app and the components using this link:

* Coolstore Prod Project Overview at

`https://master.[EVENT-NAME].openshiftworkshop.com/console/project/coolstore-prod/overview`

![Prod](https://lh6.googleusercontent.com/NIPhwQAJlVkceOhPLBWJf2n43HYKJhxvm1cvD4Pl2988BzSnqaaCEYzEnAd0HUMZ4mHHixUkaV5O70kW3gNqC-50F0IKwKV8QNw5_YYf_Kfxcu7bozdY_Wj1ej92S3l-YLYQMCCf)

You can see the production database, and an application called _Jenkins_ which OpenShift uses to manage CI/CD pipeline deployments. There is no running production app just yet. The only running app is back in the dev environment, where you used a binary build to run the app previously.

OpenShift, using Kubernetes health probes, offers a solution for monitoring application health and try to automatically heal faulty containers through restarting them to fix issues such as a deadlock in the application which can be resolved by restarting the container. Restarting a container in such a state can help to make the application more available despite bugs.

Furthermore, there are of course a category of issues that can’t be resolved by restarting the container. In those scenarios, OpenShift would remove the faulty container from the built-in load-balancer and send traffic only to the healthy container remained.  


There are two type of health probes available in OpenShift: [liveness probes and readiness probes](https://docs.openshift.com/container-platform/3.9/dev_guide/application_health.html#container-health-checks-using-probes). Liveness probes are to know when to restart a container and readiness probes to know when a Container is ready to start accepting traffic.

**Only execute these steps with your Jenkins has warning messages or errors. It's not required if its running well**.

Now we need to increase the timeout of readiness and liveness probes. Click in the jenkins application link.

![](https://lh4.googleusercontent.com/YxN8T3smd2z2lysAYd7baJlVFQuBmCU_bSzA2R5BSNB4wjtwcYv1c2YsV19S2hQe_fDnQITAmCrj8U-f8sMTDimokTD-5pWvwLXU0pM03QMlihv7uHhb5PZKCeTckmy5mk7pPrhW)

Then, go to **Actions -&gt; Edit Health Checks**

![](https://lh6.googleusercontent.com/9FfxY9HalAjWGZjKNcG86NRCp2rOOaDYuVtQq2zoNUmMOUf7piemAjYiibvhj_HaJ_i5yJDpLbiL6te1USoNr7hk_yH9RyHV3BC1pZg8G1yYyAJXlbinbFsBqnvMlz12UYw-mt21)

Set 480 timeout seconds to both readiness and liveness probes.

![](https://lh3.googleusercontent.com/LQF78Cc_V0GalJ7gEaMVl8mFQOAeOze6wsI4UJpSxAmhX_OuoEosCSG-Yx35G_oQRFH56ERKgNK9l92QB-Pd_n10SGmeMYH4V4QY4BDXezh5QrMo81cUI6-Ky0_8vD7LeM9LHbvF)

After that, click in save button.

![](https://lh4.googleusercontent.com/at9kfi3p0CkJkjJJAxcD2LbUCe2GDTGHEBt9347c1QAIxCJ5MCDS3y32Kh9UHusFRVjGNIZ_v3LbRgnK5OmnUKsqmXa2dhuoMpGoHMuDxt0UDrnsgiHJgAFd3F3Tfw87nLtCSQlk)In the next step, we'll promote the app from the dev environment to the production environment using an OpenShift pipeline build. Let's get going!

