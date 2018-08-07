# Verifying the Dev Environment

## 1. Verify Application

You can review the above resources in the OpenShift Web Console or using the oc get or oc describe commands \(oc describe gives more detailed info\):

{% hint style="info" %}
You can use short synonyms for long words, like bc instead of buildconfig, and is for imagestream, dc for deploymentconfig, svc for service, etc.
{% endhint %}

{% hint style="info" %}
Don't worry about reading and understanding the output of oc describe. Just make sure the command doesn't report errors!
{% endhint %}

Run these commands to inspect the elements:

```bash
oc get bc coolstore
```

```bash
oc get is coolstore
```

```bash
oc get dc coolstore
```

```bash
oc get svc coolstore
```

```bash
oc describe route www
```

Verify that you can access the monolith by clicking on the exposed OpenShift route at

http://www-\[your-username\]-coolstore-dev.apps.\[EVENT-NAME\].openshiftworkshop.com to open up the sample application in a separate browser tab. Full link is listed in Requested Host attribute.

You should also be able to see both the CoolStore monolith and its database running in separate pods:

```bash
oc get pods -l application=coolstore
```

The output should look like this:

```text
NAME                           READY STATUS RESTARTS AGE
coolstore-2-bpkkc              1/1 Running 0 4m
coolstore-postgresql-1-jpcb8   1/1 Running 0 9m
```

##  ****2. Verify Database

You can log into the running Postgres container using the following:

```bash
oc rsh dc/coolstore-postgresql
```

Once logged in, use the following command to execute an SQL statement to show some content from the database:

```bash
psql -U $POSTGRESQL_USER $POSTGRESQL_DATABASE -c 'select name from PRODUCT_CATALOG;'
```

`$POSTGRESQL_USER` and `$POSTGRESQL_DATABASE` environment variables are already defined. If you want to check tem run the following commands:

```text
echo $POSTGRESQL_USER
echo $POSTGRESQL_DATABASE
```

You should see the following:

```bash
         name
------------------------
 Red Fedora
 Forge Laptop Sticker
 Solid Performance Polo
 Ogio Caliber Polo
 16 oz. Vortex Tumbler
 Atari 2600 Joystick
 Pebble Smart Watch
 Oculus Rift
 Lytro Camera
(9 rows)
```

Don't forget to exit the pod's shell with exit

```bash
sh-4.2$ exit
```

You can also login in the management web console and run the same SQL query from your browser since Openshift provides a terminal window. Access [https://master.\[EVENT-NAME\].openshiftworkshop.com/console](about:blank) and follow the steps below:

Choose the **Coolstore Monolith - Dev** in the projects menu.

![](https://lh6.googleusercontent.com/GViWSHUTAPwIsULR1I8oPMIeXXiJa9FI8h8_I7ycwKkhm2qgKXpcyuclgklG7QlwK8u1MsQC6h5pN1QTyYp9eRRHcdQuwZHFfJeTqYl3AAiWJJbTJ3Xg999JnxUV1hXRK9ZglSeT)

Expands the **coolstore-postgresql**

![](https://lh4.googleusercontent.com/xbKuOiWkyhZPU_VfLDv1RC7a4g6-v3u31GjbMkisZ-raXykc1KNap7OMQ08FdkBIUW-6zuIE7nLWFCxDXt9CaGPiM5f1pVVPrIXWuuQBUVqkEpV4xK-LKbND-kXIDB5MtOLKSDQt)

Click in the number of running pods

![](https://lh6.googleusercontent.com/5xkCu83LkY7XRYE3zbfLTAfqxp3qop3dGyQ-Pl_g264OlXcvyVFfgimG-mF64Ev0-g0IipuAQpDXP48uqufFEo6rJDH60ksw01p1bRkPk0OBXqiHFjOMaPGYwcZgdv6b_y1Vj_tK)

Go to **terminal** tab menu

![](https://lh4.googleusercontent.com/7vy4-qQUpz8946j3AE0rpy4z8QHHPd69wK6yzySkJY-IVJI_Zh2LmI5QBHdJqdeDqYR5xfaukiXucULTUKa0iI4Qr-OeUFGoHjbonw7W0G57y2ywzeZBxoJsg68PTyLl5kvgXwVB)

Run the same SQL Query command you executed previously:

```bash
psql -U $POSTGRESQL_USER $POSTGRESQL_DATABASE -c 'select name from PRODUCT_CATALOG;'
```

\*\*\*\*

![](https://lh5.googleusercontent.com/O8RqZhUvmnvuemhmvdSC5IPKhmPY12N7KYioci4nHrwILMarP8OZUUGlvZ2uGTCaSzTpVZECoTsu4C3aYfQVPXsRtn4Jm0WZsGKICrO3NRNPyLXFsCvkABSZTABGKGwHh_k_nBaN)

Also, explore the Environment tab also available and see the same environment variables you’ve seen before.

![](https://lh5.googleusercontent.com/t7FE0ybJXM4cfCr1GZTvhuU2Dr27CNgsAuTJ6eUMB2XW7_KoS9uJvsdlMzQM5BevuDNta2Jhazig23aUwV-JpDVkVGNBl3Bg6Mq03GtjJ8TuZnW00fYGAePdjnGWrdtipO1pemX1)

With our running project on OpenShift, in the next step we'll explore how you as a developer can work with the running app to make changes and debug the application!  


