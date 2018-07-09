# mscrmbugfixes
Microsoft Dynamics CRM/365 Bug Fixes for On-Premise Deployments

The following bugs were discovered using mostly Chrome browser. The changes are not supported although they are actual bug fixes which makes the system work, so use it with care. Remember to re-implement them as soon as you've done an upgrade to a newer version.
The bugs have been confirmed by various people I work with on a daily basis and is applicable to all CRM on-premise installations.

## 1. Double Click Copy Paste bug (Microsoft Dynamics 365 v8.2.1.176)
This bug happens when a user double click to selects a readonly field on a Form, presses Ctrl+C and Ctrl+V in another/same application.
This bug however does not happen when you slowly select from left to right or right to left, which nobody uses anyway.
For example, we have a field on the **Account** form called `accountnumber` which has been changed to read-only.
If the value is *ACTBBDC3* and a user double click to select it, copies it, it is copied to the clipboard as 2x values, **ACTBBDC3ACTBBDC3**  
When we inspect the element with Chrome Developer Tools, we can see that Microsoft is using two fields to show the value, both of which is visible.

![Image of Account](https://raw.githubusercontent.com/riaandelange/mscrmbugfixes/master/images/doubleclickexample.png)

Modify the file `Microsoft Dynamics CRM\CRMWeb\_forms\controls\controls.css.aspx` so that the class `.ms-crm-div-NotVisible` has a display of none value.

```css
.ms-crm-div-NotVisible {
    position: absolute;
    <% if (CrmStyles.IsRightToLeft) {
        %> right: -10000px; <% }
    else { %>left: -10000px; <% } %>
    top:auto;
    width:1px;
    height:1px;
    overflow:hidden;
    display:none;
}
```

Save the file and do an iisreset.


## 2. Email Editor with  height of 288 pixels
This bug happens when someone is creating or replying to an email activity. The default height for the email editor control is 288px which has been hard-coded by Microsoft developers. There is an element called description_d and it has a data-height of 288 which is then used by the style height of the same element. You can change the values to whatever you like, i.e. 800.

To fix this, create the following OnLoad and OnSave events on the Email form.

```JavaScript
// email.js v1.0 - Copyright Riaan de Lange 2018
// Download the latest version from https://github.com/riaandelange/emailjs
// Fixes the default email editor on the CRM form Email from 288px to 800px height so you can see more details in the Email control.

function FormOnLoad() {
    ExpandEmailDescription();
}

function FormOnSave() {
    ExpandEmailDescription();
}

function ExpandEmailDescription() {
    parent.document.getElementById("description_d").style.height = "800px";
    parent.document.getElementById("description_d").setAttribute("data-height", 800);
}
```

Save and Publish the form changes.
