# Migrate and run the project

Now that we migrated the application you are probably eager to test it. To test it we locally we first need to install JBoss EAP.

Open a terminal window by clicking on **Applications → System Tools → Terminal**

Run the following command in the terminal window.

```bash
unzip -d $HOME $HOME/Downloads/jboss-eap-7.1.0.zip
```

We should also set the **JBOSS\_HOME** environment variable like this:

```bash
export JBOSS_HOME=$HOME/jboss-eap-7.1
```

Done! That is how easy it is to install JBoss EAP.

Return to JBoss Developer Studio and change the perspective to JBoss by clicking in the icon ![](https://lh5.googleusercontent.com/vdRv5WRQsjib0TikMYPi4MYa8CG-bQetrsG3ov7vzf879Sh03D1kfM0OZiVL6uxwRg6t6DnxaOmz1IMNIIPu2-ZIUx6ISRZFlRBpT0T6gNMChUpv0h-gpP8vKQ7UpBQpjPDIGI4H) or navigating on **Window → Perspective → Open Perspective → Other → JBoss**

Open the `pom.xml` file, and click in pom.xml tab.

![](https://lh6.googleusercontent.com/2p5poOgwBRGQWvnzlm7_5U5yxuWjuaNdanATlMnh1nCiRlpB9LdZ-p2FGoVCbqbI72-RHqcTEAb1e4-2QUHdncax8s-DmhZyLXJ9-UU761_vLtNm79526v2R0l1fk8ZTDYBWgpXd)



