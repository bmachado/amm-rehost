# Deploy the monolith to OpenShift

We now have a fully migrated application that we tested locally. Let's deploy it to OpenShift.

1. Add a OpenShift profile

Open the `pom.xml` file.

At the `<!-- TODO: Add OpenShift profile here -->` we are going to add a the following configuration to the pom.xml

```markup
<profile>
  <id>openshift</id>
  <build>
      <plugins>
          <plugin>
              <artifactId>maven-war-plugin</artifactId>
              <version>2.6</version>
              <configuration>
                  <webResources>
                      <resource>
                          <directory>${basedir}/src/main/webapp/WEB-INF</directory>
                          <filtering>true</filtering>
                          <targetPath>WEB-INF</targetPath>
                      </resource>
                  </webResources>
                  <outputDirectory>deployments</outputDirectory>
                  <warName>ROOT</warName>
              </configuration>
          </plugin>
      </plugins>
  </build>
</profile>
```

2. Create the OpenShift project

First, open the OpenShift Console in the browser:

`https://master.[EVENT-NAME].openshiftworkshop.com`

![OpenShift Console](https://lh6.googleusercontent.com/adRMjMULPLSoIb1GcqdIhMmcTYURlU5AASF74_8YhhVgOu0t4FQXNhR4kE8Al6Rf_rRg844CO_Hkv_18hR2dvvh5cW17_iUbSd72NllEBl6tgIdFOFtWzyP5AjRYBqP4sludY70R)

Login using:

* Username: `user[YOUR-USER-NUMBER]`
* Password: `openshift`

You will see the OpenShift landing page:

![OpenShift Console](https://lh4.googleusercontent.com/zPgMUrk7SEe4siVLZiiAKBjzxIdZzJeFPlrmAgptLLejQkTifuIIXJ4s5pGac5bgThr_0wXYF12haCAQ_fIGhm6DCzbxgMjAeClr9YjA57BDCcRmYGl53ipFKGY1v_mFl2DmOo6M)

Click Create Project, fill in the fields, and click Create:

* Name: `coolstore-dev`
* Display Name: `Coolstore Monolith - Dev`
* Description: _leave this field empty_

{% hint style="info" %}
NOTE: Replace \[your-username\] with your login in openshift. \[your-username\] before coolstore-dev is required since all students are using the same openshift cluster and it doesn't allow to have more than one project with the same project name.
{% endhint %}

{% hint style="info" %}
NOTE: YOU MUST USE `coolstore-dev` AS THE PROJECT NAME, as this name is referenced later on and you will experience failures if you do not name it `coolstore-dev`!
{% endhint %}

![OpenShift Console](https://lh4.googleusercontent.com/2XYgas85vGlCgOi9RRHnpHwSddE6g5q-a1PxxhxHZHo8Ipt0B8iDK3F5xs8x8iVXOsvRBbRDCl-OvtNAFGNN2M0IKrbjN11O5y2mJE8t1nfCk_tsP4W863we1gCueBanbJFBR3hl)

Click on the name of the newly-created project:

![OpenShift Console](https://lh3.googleusercontent.com/sKgjYzFDDKt7y_yEqGT-vjcgsCaWyZvu5OvMbvWUqPoicR2hfVL57nt-ARBBGqrA22Q5eBqNPVlI0jygpS6flWrbxXOUIaxkUgrFUJLru3VoNCEbwUtZ20KbA_Own3KellSv4JrP)

This will take you to the project overview. There's nothing there yet, but that's about to change.

3. Deploy the monolith

We'll use the CLI to deploy the components for our monolith. To deploy the monolith template using the CLI, execute the following commands:

Firstly, you will need to open the terminal window and make a login into Openshift Container Platform. The web interface provides a ready to use oc command with the token needed.

![](https://lh6.googleusercontent.com/ka14YTWqsxGnbVNVc4x8f2-79Md4pppNCzTDaL0A0nrYHEQBBwz-v8L4_yiYW2r4yaWxXsfB9RhFXNc2gPw98llQgM42l4HCWlzOSKsljCvA2mE3iCrpN1wzmuGMbNUIiQXE1bnB)

Just paste it in the terminal window:

```bash
oc login https://master.[EVENT-NAME].openshiftworkshop.com --token=apslfkwikdk
```

Switch to the terminal window in project you created earlier:

```bash
oc project coolstore-dev
```

And finally deploy template:

```bash
oc new-app coolstore-monolith-binary-build
```

This will deploy both a PostgreSQL database and JBoss EAP, but it will not start a build for our application.

Then open up the Monolith Overview page at

`https://master.[EVENT-NAME].openshiftworkshop.com/console/project/coolstore-dev/` and verify the monolith template items are created:

![OpenShift Console](https://lh5.googleusercontent.com/a-nlzEUCDZ7NH1lgi7-eGl14_VE3q5Bxx15u0H3LK0tJJyZp6lYt0gNJKxiVUMc7PLGJigBnpaNw9c1TDjyXF-j6QhLV01xjg7Q3bnA4gGCgkzn7x5Q00zLQv1uFyowlt9woI5o4)

You can see the components being deployed on the Project Overview, but notice the No deployments for Coolstore. You have not yet deployed the container image built in previous steps, but you'll do that next.

4. Deploy application using Binary build

In this development project we have selected to use a process called binary builds, which means that we are going to build locally and just upload the artifact \(e.g. the .war file\). The binary deployment will speed up the build process significantly.

First, build the project once more using the openshift Maven profile, which will create a suitable binary for use with OpenShift \(this is not a container image yet, but just the .war file\). We will do this with the oc command line.

Build the project:

Using JBoss Developer studio, right click in the pom.xml, navigate to **Run As → Maven build…**  
  


![](https://lh3.googleusercontent.com/YBzKnjhgFNHRAEVEWnnH_8gFv3XcGgmvT322FXSZHt_DXRA177MXC-6pIZvXOJleJ-UpWFuxcHRUtQqFm2GFp6I_7fO--f_kb9bvNrRZj5W2bVKQ74b-qHnokSQCakcCI1bLoOUd)

Add clean package -Popenshift as the goals to this maven build

![](https://lh6.googleusercontent.com/KQoBMMUAzRBNwKFAol1dTxDDzZzSncsH2LphRnyXioL9QZJv3sSTEiohKoJAP09vnF-aX5tY4bZYy-bETlkRSCQY3tSv0r52fxgH69jSwps3BF3CgaMuT4sNsWG5fCdZPwoQme0v)

You can also run the command below in the terminal window if you prefer

```bash
mvn clean package -Popenshift
```

Wait for the build to finish and the `BUILD SUCCESS` message!

Go to Openshift Explorer tab and add a new connection using its wizard:

![](https://lh5.googleusercontent.com/btYqANIenDwLElY9OX_Yz54pK0qyHIDVCDzMmJ2GhoneDT5Y7SJWhnLMgnlFd2kvUd4kAQN4lXNAF9qJ-ybNl_Tn_3kZbyu9muV_EgxrbzD3R0dEUyrJ8sRmdWspU5tmWMxSc9Qg)

Enter the master node url provided by the instructor \([https://master.\[EVENT-NAME\].openshiftworkshop.com](about:blank)\), select Basic protocol and enter with your userN and password openshift.

![](https://lh4.googleusercontent.com/amAHzzCcVOpGB4_ffU2mgpYMBuBQG9PAnZDjNJwXoRW2aY0b9gCMOBXrrF0kf7QfWA5BUzuQo5CLCgHb45HI4fwD6uITERrWbQSZBhEu_Gr_EKqBG5HjNPZbx3An4yCppIfG0McQ)

Accept the SSL certificate.

![](https://lh5.googleusercontent.com/CE3kXnGj8PDeqCpmxTBR0G8zugsdzC9eWDoMEW30E33mN8dIDDi9CFNbeF4MrTW1qp4sWd__axY4Wf4YmPhoJTS_UIPjI3SIAkzM4pxYwZXNnemVehrKDbfbk-DXXNK5zDQ0XgAE)

Then click finish.

You will see the Openshift connection in Openshift Explorer tab.

![](https://lh3.googleusercontent.com/dHj9xaCgij3OgtG4bGiWm8EWVzGPqltv6IiV9We1x-Am5TGP5tSQTBeAaj_FdzjuqKjmvZpVir_BSiDpZMkXVCjKuNTSD28Y-Bsso5Lc7slOm0GHnFI3HfVb4b6Z3x_So1cwa4vt)

And finally, start the build process that will take the `.war` file and combine it with JBoss EAP and produce a Linux container image which will be automatically deployed into the project, thanks to the _DeploymentConfig_ object created from the template.

In terminal window, certify you are in the coolstore-dev project

```bash
oc get project
```

Start the coolstore application build

```bash
oc start-build coolstore --from-file=${HOME}/projects/monolith/deployments/ROOT.war
```

![](https://lh6.googleusercontent.com/lMUUPe8HEefSL8W9LPk3d0k9t60Rn3arMYxeHEqCqKI7kxPOkfbZ6OjMU0neZsRiZaNqsygRFk0JiTnTubODqNFybqoiPYCn8fPbyNPW2Sdz_OYxhQUvYgUVYJtjXohxE_BKG4Wo)

Check the OpenShift web console and you'll see the application being built:

![OpenShift Console](https://lh5.googleusercontent.com/1oYV54VqF6du_BIU1-EM25RP0wAGobb7e_47NTz0EmsXAK7CFLyAbPkPe1QXVfpznwcaqso6_BWcId6djHX2IkVHmcz1j7n40AGqgFdqOp43ozEHL_8RN9uGpy5PuONohgWVcGQG)

You will also see the Openshift Explorer tab in JBoss Developer Studio updated.

![](https://lh4.googleusercontent.com/qZE8TGeqvWBrWqtGVjHtYr05GGHXHu6mUzZckVGCqBraslkgxESdHR5Jq2Bh1gi6UGFJJFP2yMyI-nhucFf8pYmXYrrfNc2o3inQLOXDWRkKAiISv-0J4FV16mpugOftiuZlxRhH)

In terminal window, wait for the build and deploy to complete:

```bash
oc rollout status -w dc/coolstore
```

This command will be used often to wait for deployments to complete. Be sure it returns success when you use it! You should eventually see replication controller "coolstore-1" successfully rolled out.

If the above command reports `Error from server`\(ServerTimeout\) then simply re-run the command until it reports success!

When it's done you should see the application deployed successfully with blue circles for the database and the monolith:

![OpenShift Console](https://lh3.googleusercontent.com/qeJj3G-Ae_gato3Un3NYgz6n71cb05Cjr2px9cRzGwxfcUMJXKI_EWHzg82Dm9hbO_hFEqWYq3Lqq7vXwtZr0e1LCWjZqc9ZPlf3SjQBpZfQEQBlCkoSHWT1otezDLsPXqwQLozw)

Test the application by clicking on the Route link at

`http://www.coolstore-dev.[EVENT-NAME].openshiftworkshop.com` , which will open the same monolith Coolstore in your browser, this time running on OpenShift:

![OpenShift Console](https://lh4.googleusercontent.com/l1yoAXLaHqQ9fj08CIipI1kZvmXrkqwltW0hwwDUk7gzLM42e0n8FjEKq4mDYlW5cvZsMJkZ8vBXlxM2P6dwLqrzG68yjj8ZuAHv9LuBS1e-f_qvdJwm9rOBFyFJyOQanLUnc8z2)



