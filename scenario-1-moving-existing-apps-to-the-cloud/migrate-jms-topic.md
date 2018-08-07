# Migrate JMS Topic

Our application uses [JMS](https://en.wikipedia.org/wiki/Java_Message_Service) to communicate. Each time an order is placed in the application, a JMS message is sent to a JMS Topic, which is then consumed by listeners \(subscribers\) to that topic to process the order using [Message-driven beans](https://docs.oracle.com/javaee/6/tutorial/doc/gipko.html), a form of Enterprise JavaBeans \(EJBs\) that allow Java EE applications to process messages asynchronously.

Much of Weblogic's interfaces for EJB components like MDBs reside in Weblogic descriptor XML files. In project explorer, open `src/main/webapp/WEB-INF/weblogic-ejb-jar.xml` to see one of these descriptors.

![](https://lh5.googleusercontent.com/M7q5fWLy1suvD4OqmRfhU1jxTZTZ-U6Ps17jEHKuC_dHMWxHh4SHjMCEfPuWroVjQuoKEkDK71EKR0FgKPFvU2ktfpaU8iuLjoRUa-V1cdrUHCeZuu-NJJsCHbWMA2Hl-IHJYr5y)

Another possibility to open weblogic-ejb-jar.xml is to use the ctrl+shift+r shortcut and fill the search with weblogic-ejb-jar name.

![](https://lh4.googleusercontent.com/LCu_uRHHfNeQqvrwzcyW0VP70bhTWyjLA6wOnukPrgO3YJpytm_j-Vzx1kf5sS0LeUjNjOAku3vkGUbC-41C_Kxp44x0Is54UQXHBjjK0RcprQYrauA5xfbkXjret7irtAWd-RVA)

There are many different configuration possibilities for EJBs and MDBs in this file, but luckily our application only uses one of them, namely it configures `<trans-timeout-seconds>` to 30, which means that if a given transaction within an MDB operation takes too long to complete \(over 30 seconds\), then the transaction is rolled back and exceptions are thrown. This interface is Weblogic-specific so we'll need to find an equivalent in JBoss.  


1. Review the issues

From the RHAMT Issues report tab we will fix the remaining issues:

* Call of JNDI lookup - Our apps use a weblogic-specific [JNDI](https://en.wikipedia.org/wiki/Java_Naming_and_Directory_Interface) lookup scheme.
* Proprietary InitialContext initialization - Weblogic has a very different lookup mechanism for InitialContext objects
* WebLogic InitialContextFactory - This is related to the above, essentially a Weblogic proprietary mechanism
* WebLogic T3 JNDI binding - The way EJBs communicate in Weblogic is over T2, a proprietary implementation of Weblogic.

![](https://lh6.googleusercontent.com/uCN5Inu6bJPFsMrLi1KOzSLJBtV12fUAGltJzlESWVJH2rJuRPYs5p0oUvnJKdhRnkfIcHkx_gUOtTb83V0lZXaGHFQY2muwuakmP4NYhM4DGcIVimGa1OTstveH30kBl3DI7reI)

The same remaining issues are listed in the Issue Explorer. Navigate on it until find them.

![](https://lh6.googleusercontent.com/-eIalxNfW-3g3lIo1ccGTA9PK7X6KYksbtSlhiAkRpcKIO7a5hoZcFCATrJUy6JXuBgoQ_p3DSaPIRp4WUM-BH05e7gTXg97oCrAnpaiEYyeRohVqDBm5laTogR5pB9by7WGk2jS)

All of the above interfaces have equivalents in JBoss, however they are greatly simplified and overkill for our application which uses JBoss EAP's internal message queue implementation provided by [Apache ActiveMQ Artemis](https://activemq.apache.org/artemis/).

2. Remove the weblogic EJB Descriptors

Select `weblogic-ejb-jar.xml` and press delete key or right click the file and click Delete option to remove it:

`src/main/webapp/WEB-INF/weblogic-ejb-jar.xml`

Also, in project explorer, remove `src/main/java/weblogic` folder

![](https://lh5.googleusercontent.com/doyMOG9zR0BfLZds9S4S8aL_dPUWPl887mxu_TDoBRmo0ZMe-q0xD718BeypZeZSWdwKS7r4UVdJANDZuveZwKHk8MZ79UsNwfS30hUZYAiiDrCCrxTs5MZw0eNstzoc3Lhq2DHY)

Confirm the operation by clicking on ok button

![](https://lh3.googleusercontent.com/QvimE5hVOdtevf4KKlZJgvatwBQJVIZrpX0qotLdAG51tNPZaTGUNVTfuNobfBREbGYt5ifcGORq1Nu6XomBd4gCrAcasJ0JjesmMiRlRLyrWufzGmvvo-luS71ODlsuvzLbP4O1)

3. Fix the code

In Issue Explorer, double click in Call of JNDI lookup issue, it will open the associated source code line in _InventoryNotificationMDB_ class.

![](https://lh5.googleusercontent.com/3o8KP04izZ0qE0zPow8uUNiK9JRcyIqp81n1E8pJO957GZ0uHFpL1kAkY3JGWiBuO11qm1jFgHUDidPmDr1LQICiSOn6ZnvB45GhUJa7n1PrbxA8Dzn7DmhssO9Fbce5eGkql25r)

As described in previous steps, you can check the Issue Details to get tips and review a knowledge base article before migrating.

Fix the code as following:

`init()`, `close()` and `getInitialContext()` methods aren't needed anymore and can be removed.

MDB class will receive `@MessageDriven` and `@ActivationConfigProperty` annotations according to Java EE specification. So, add them to the class definition.

```java
@MessageDriven(name = "InventoryNotificationMDB", activationConfig = {
        @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "topic/orders"),
        @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
        @ActivationConfigProperty(propertyName = "transactionTimeout", propertyValue = "30"),
        @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge")})
```

Then, press ctrl+shift+O to solve class and annotations dependency issues or navigate to **Source → Organize Imports**.

The last thing to do in this class is to remove private class attributes listed below:

```java
private final static String JNDI_FACTORY = "weblogic.jndi.WLInitialContextFactory";
private final static String JMS_FACTORY = "TCF";
private final static String TOPIC = "topic/orders";
private TopicConnection tcon;
private TopicSession tsession;
private TopicSubscriber tsubscriber;

```

The complete InventoryNotificationMDB source code is listed below:  


```java
package com.redhat.coolstore.service;

import com.redhat.coolstore.model.Order;
import com.redhat.coolstore.utils.Transformers;

import javax.ejb.ActivationConfigProperty;
import javax.ejb.MessageDriven;
import javax.inject.Inject;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;
import java.util.logging.Logger;

@MessageDriven(name = "InventoryNotificationMDB", activationConfig = {
        @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "topic/orders"),
        @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
        @ActivationConfigProperty(propertyName = "transactionTimeout", propertyValue = "30"),
        @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge")})
public class InventoryNotificationMDB implements MessageListener {

    private static final int LOW_THRESHOLD = 50;

    @Inject
    private CatalogService catalogService;

    @Inject
    private Logger log;

    public void onMessage(Message rcvMessage) {
        TextMessage msg;
        {
            try {
                if (rcvMessage instanceof TextMessage) {
                    msg = (TextMessage) rcvMessage;
                    String orderStr = msg.getBody(String.class);
                    Order order = Transformers.jsonToOrder(orderStr);
                    order.getItemList().forEach(orderItem -> {
                        int old_quantity = catalogService.getCatalogItemById(orderItem.getProductId()).getInventory().getQuantity();
                        int new_quantity = old_quantity - orderItem.getQuantity();
                        if (new_quantity < LOW_THRESHOLD) {
                            log.warning("Inventory for item " + orderItem.getProductId() + " is below threshold (" + LOW_THRESHOLD + "), contact supplier!");
                        } else {
                            orderItem.setQuantity(new_quantity);
                        }
                    });
                }


            } catch (JMSException jmse) {
                System.err.println("An exception occurred: " + jmse.getMessage());
            }
        }
    }
}
```

Remember the `<trans-timeout-seconds>` setting from the `weblogic-ejb-jar.xml` file? This is now set as an `@ActivationConfigProperty` in the new code.

Your MDB should now be properly migrated to JBoss EAP. Plain Text 

