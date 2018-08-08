# Migrate Application Startup Code

In this step we will migrate some Weblogic-specific code in the app to use standard Java EE interfaces.

Follow these steps to use the Eclipse plugin to identify and resolve migration issues.

1. Import the monolith project \(`~/projects/monolith`\) to analyze into JBoss Developer Studio. Navigate to **File → Import → Existing Maven Projects → Next**

![](https://lh6.googleusercontent.com/_b4BIqxsE3Uk54zJMytDMhG8lY48w3CJSl2LnTMInIRNE72SJqh9uRhh0IRgeB3yFsfGdoCNoBT68a_p6QSJvJLD91XE4x9vNunGkcjiyADgGbq4lP24Qa_bAJExnfWg1AHDr-5K)

2. Browse to `/home/developer/projects/monolith` and click on Finish button

![](https://lh6.googleusercontent.com/K8Z0BcJKlCANjXe44uztMwe8M0zCWQSfhFPCRbWifjO81dJFTtfrlqYhJMY35awRfxmDAGCrMUuD0S6RGM-o3LSGsekkK1TNc_TPiwqqg9NzjGhhM28ry5UQf_5l9gsS5ViO2k84)

3. Create a run configuration. From the Issue Explorer, press the RHAMT button \( ![RHAMT button](https://lh5.googleusercontent.com/CeYYv5ubA-Ep27GTI0ZAPgf0V2HzvwBGBzg7UE2Zyc9yKbViC0Vc6flTS31KU3ap91G4Gyw9MpnVzgKeOutajFtIMEmzrAAsfzedkjPjLtAkP-b6ZepX2oAREgoP5LRcYWWhz_pG) \).

![Select RHAMT button](https://lh4.googleusercontent.com/OquVB6r6zFb80GgeZXvTkQQnWGETfbVEtfzCwRLG5mWPcIXNp70zK6GLAarQk9ib0t9RYeJHESc3FQgy7dEpulcOsb_RpCgwqnegmNl5vO1K4OxvDDkwcLVwOQajL32H3SQADtoX)

4. Add the monolith project to RHAMT configuration

![](https://lh3.googleusercontent.com/I4B0AkdKH6B-u1ARSqsMyI_Vownw99e37Yaj0JQ8ekNs1-9PEoHZnuN60zbVOaREME8GX3i5xu1Tf6tJoDYyuPqrFcQP9QxbBHMP8Cp18nK8h0XvO_RpmjPTjAsnckBWGjECCm4D)

5. Click Run to execute **RHAMT**.

6. Wait until the analysis reports are generated

![](https://lh6.googleusercontent.com/bM7M-2fRLsaIBokO2JCAsUcKt2MWW-ahtVHi-27RDkP_4zHm_qNqG8tiEwdYlYjpF8PUc0oOEW39bZVTYXIQDT7gamIBNYv6mJQsfvAi14K5aJvMAD5rWbFfybaSSfAft21RioPk)

