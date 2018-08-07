# Configuring JBoss Developer Studio with the application

You can also control the JBoss EAP runtime with JBoss Developer Studio by following the steps below.

Before starting, we will need to undeploy the application. In the terminal window, run the following commands:

```bash
cd ~/projects/monolith
mvn wildfly:start wildfly:undeploy wildfly:shutdown
```

Add the server installed in the previous step in the server tab by right clicking in the blank space, navigating on **new → Server**.

![](https://lh5.googleusercontent.com/dC6-WBxn4KNAxJ6F9Nhh_XOyv_4j0uLz09Bq23AAYr-4zJDILdltEV3NxBuXkTT2wvA5QNeAEneBZ2gHh2fV_MAt0d2Qidxj-gJ7vRVnyriHWvLgMVHe1WDR10IrFxU9Wj9z1SHw)

Select Red Hat JBoss Enterprise Application Platform 7.1 as the server type and click Next.

![](https://lh5.googleusercontent.com/l6Vda8rnmzNYjB9-KxPyu6FpiapTE0cYd5J19EsDEep9YP5EcDbBGfnFJhJK_xgUQFoY8kQqSRZhU49W9fp9CbySfWahuwP0BTIt1GVXoMWG0rK0E5JacOPfaaERgsqvf5FC99zL)

Click Next again. Browse to JBoss EAP 7 installation directory `/home/developer/jboss-eap-7.1`, select standalone-full.xml in the configuration file and click next.

![](https://lh6.googleusercontent.com/Vn640o8FfFrdgGB-PHEr-xRIXk7Tg3QoAwX00yqIqAK3daRPSrcca6RHSz1U7Q20pmpwA4WsICGFUg3npDhrZAjROe31L-f72qkjGNlYm4OHd9LCr6ba4YyPCO7CWpxbUt6ElCBu)

Choose monolith\(ROOT\) application, click add button and finish.

![](https://lh6.googleusercontent.com/PipdcEFW1V1XlIgyZoFuUCSLtXz2n7MXKQp81syL0rn_zJ-b5ZDZfF2Q-EXTeOIIUMklmjaVab_Fl0hh-bQLsowT4l0ksCkLBLw6i8yZcE5ShVrjlhYYp6iBQ1cI2ZEpcynEmDMb)

Click in the Start button to run EAP instance.

![](https://lh4.googleusercontent.com/lU80L0GP-NR7RpFj7qjNX5QormVRuCWiaUoMEpmSIXrG0Xx5Cfi7fRTTcg88lgtkrTKe_Xt06aBKIUdCuQP52zdE-7qTw6yo81_6ggrUGQrEl_Xc-j9fEw4omhFykv9J8Ekn2tdD)

Check if EAP has started successfully in the console

![](https://lh6.googleusercontent.com/bmU-bof-6FxcMGJ63_39LfBo0-GwydY84FaP1YmkBtKF2Ko9bLRm-9hJfHUzUhM0d8V8YF_WRzdlUQWu7Dv7PcIR01qTaW8L63TDCMtTe-W7QJ_AqWxxh6bJVOtv-oXHmMpQ8O55)

Check again the coolstore application in the web browser accessing

`http://localhost:8080`

Then, click in the stop button to shutdown JBoss EAP.

![](https://lh6.googleusercontent.com/P-ln9IhPyJT22Xa77PhtvvMUPIJo42gqt-A2J0B9LTJEkP2pZTeZYeB4E7dqcDcfuisoOisE_ueFob2ggV3VJ5ZN39jUDCoikwZcax_iAtKhAxTd32LEWReSQ5ln-jUyeMoPo3-u)

