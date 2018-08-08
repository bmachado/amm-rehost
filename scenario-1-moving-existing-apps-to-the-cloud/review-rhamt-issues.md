# Review RHAMT Issues

Use the Issue Explorer to review migration issues identified by RHAMT.

![](https://lh4.googleusercontent.com/eji3UK8EKiU_o5zLPiB95ZArsIIh9GhF-mkLES_DCqad04YzAD7nFMvKCIS1BBIfeddrWO7tl57u-Wa8HwghpVJA8iLPkbkTI7plK1riIn6QNdJNsUcuLmfanTtJcpB2TYwsldF6)

Now we need to migrate the monolith app to Java EE standards.

1. Review the issue related to `ApplicationLifecycleListener`

Group the issues by severity only:

![](https://lh3.googleusercontent.com/gXJmg6LTbUFe7dgxh6gdtLmSMVXW_04LxngVMnfcOXvFh26Az_NvHkbrRxegwZU4m96EHZLEZUaz72GfI0JFvOS0qeT3RshBfByHLx6XVsCnjVEBxZHZ7hujHqJIk4g5UIoVPDM9)

Navigate in the Issue Explorer until find the ApplicationLifecycleListener, or type the name of it in the search field.

Right-click on it and select **Issue Details** to view information about the RHAMT issue, including its severity and how to address it.  
****

![](https://lh4.googleusercontent.com/8Ku0yPEXjV6IpVHKsQP3l0ttDTF7plZzZlaMESbr0qgxphDPupMZT2JaANjfETXkElneBv-J7m-DWOdwrNDcCY3alVf4gLEP_OjjCr5D81FSxAK0S3SBEpMKp_YuW6OwQzwEypa3)

RHAMT provides helpful info to understand the issue deeper and offer guidance for the migration.

![](https://lh6.googleusercontent.com/pMRrEXOdHC3tRxLmhnpWH4XdwFvJa8fi7hQCQDzMxHS5Uy6bk5WSM5lcZOKl3bb-SpVhAKHP46CdWhovFPrdAVIWxS4-3Dmjvkpic6URXAdWvf6Bgx6-Mkw-yAHFi3cFw3vLmK1Z)

2. Open the file

Open the file `src/main/java/com/redhat/coolstore/utils/StartupListener.java` by double-clicking the mandatory issue with specific WebLogic source code.

![](https://lh3.googleusercontent.com/ZJPnOKDuGVb6oWduycY6JOd026mi7k-CqB_n3kWCkXTM9mPbP8XO4PtGSgFaOCgzrAHQnQvTj8SpKgJDmZwanS54thlMEoSfpATVIha_I0OuvVq8JaCzyFhMPOXrKg0peosapmL4)

It will open the associated class _StartupListener_ line of code which has the dependency in the editor.

![](https://lh3.googleusercontent.com/V87PjB01xXwQXg083AKe8GMqnrdh4610zMuNEJlFca8q1w8Oe81saaxDNcT5nbPj5xn_gsfeBOR3zJklYeYFiAiZs1wbSs2rbR5Yu2C3vTMfwgCWDvivcdMaCpbFKBweq___HfBJ)

The first issue we will tackle is the one reporting the use of _Weblogic ApplicationLifecyleEvent_ and _Weblogic LifecycleListener_ in this file. Edit the file to make these changes:

```java
package com.redhat.coolstore.utils;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import javax.ejb.Startup;
import javax.inject.Singleton;
import javax.inject.Inject;
import java.util.logging.Logger;

@Singleton
@Startup
public class StartupListener {

    @Inject
    Logger log;

    @PostConstruct
    public void postStart() {
        log.info("AppListener(postStart)");
    }

    @PreDestroy
    public void preStop() {
        log.info("AppListener(preStop)");
    }

}
```

3. Test the build

Build and package the app using Maven to make sure the changed code still compiles. In Project Explorer, right-click on **pom.xml → Run As → Maven Build**

![](https://lh5.googleusercontent.com/hQcQQWt-iGZzjURglDCrTpEISuSX97gsRSG2XrzOTFFOM8bfKBIPmz6kuokEehGhIiD0GfrPgOq2LQwI9iP1jenFzfe_mHvaO1NxHyz8ktvXmyw8gkje9d5IPBWSffsvdM6IJglW)

Fill up the goals with "clean package" and click on Run button.

![](https://lh5.googleusercontent.com/DM66KgrDmVqMRhmMIVseP9BWAB689K_hea01NP6Hn8mqdqDp5lZ4FqC3VCuasxaiR_zr2H0XwpqhfzvDvvkmljBdaAq66nzXblXCYsHXyNuc0RRM3hglThzy0nGsUOx80j6N94iU)

If builds successfully \(you will see `BUILD SUCCESS` in the console\), then let's move on to the next issue! If it does not compile, verify you made all the changes correctly and try the build again.

![](https://lh5.googleusercontent.com/ao7U8-WfUM0Cjnk5gQ9AQs8cvCFwixalvE5t4YC2QmkZ648Y7lHsw9B3MnrVcUpiLv7XpBTPLQUATepbwSCOVyDjI6ISeIFd8YbBmHpZ15utCa_DBj3ujM3rX_1id3dImAQ5Di0F)

Manually mark an RHAMT issue as fixed, which will mark the issue with the resolved icon \( ![Resolved issue icon](https://lh6.googleusercontent.com/sF90GjyA9vwpFcLPfmHzeyl78E-IcaNYomY9k9DjmvuJGVJxwAdDSJP2nC0O5oE1MKpBZ12MwGGZKzFUD20p1Ib-1esVTVTw7baMM0CsiXToPn2_dC6LtcUjxXTNPkepDGPHWt5f) \) until the next time that RHAMT is run on the project. To mark an issue as fixed, right-click the RHAMT issue in the Issue Explorer and select **Mark as Fixed**.

![](https://lh4.googleusercontent.com/a3oXR0aY4NT2C1m_LIc9u03-R4__-NTxLp8MXdtvwjEOzD6IS1algCQu3uETl8kad1i-PL9b6IHcoV6g1nx9dPdfnT_wCytqyf-nDVcDdv6PDuBt_AOlEPn6-kxaleEspoljwkKh)

Also, you can run RHAMT again on the project to eliminate the issues already fixed. Click in the ![](https://lh6.googleusercontent.com/wJj_9qj2epqvrNPkqBjX4ddpeg3D3ZpGP7wDTgSSVx-2XSXm3HIrQmVeSu6Ukdt75mjnbj2YS_bV8MVTwbBCzhdPI-n_UbaHgvwypydTFvTO7DsKoR_ulx4k_ynJL6p25TmagXG3)icon and then Run\_configuration

![](https://lh6.googleusercontent.com/9bplWdBwoDlVulyXM1Ae9WXp7dbkX3BEjdiQHg2sIzlK3s0OHXUddSoxc-7fI_lCsFljxkB7lcfx5n75qZBWfLM-VjrtE56tGsuJl27qMtI4JvCWUUgBVGeH-Hz0hCA99HqhYuA2)

Search by `ApplicationLifecycleListener` again in the Issue Explorer and notice that it doesn't appear anymore because its fixed.

![](https://lh6.googleusercontent.com/y82GWDqRGObks8mGlw77fKOvlyCK_BqccXTp5_MVsC312X2jC4nsSOSqR67_G1ZM05V7R_d6yEG3f5nD-C8T9jsdLxgj_v0LoXk_mPyANZ6z9jDrzV2ExJmKlf7exe84RoPvzbGz)

