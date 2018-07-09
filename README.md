# mscrmbugfixes
Microsoft Dynamics CRM/365 Bug Fixes for On-Premise Deployments

The following bugs were discovered using mostly Chrome browser. The changes are not supported although they are actual bug fixes which makes the system work, so use it with care. Remember to re-implement them as soon as you've done an upgrade to a newer version.
The bugs have been confirmed by various people I work with on a daily basis and is applicable to all CRM on-premise installations.

## Double Click Copy Paste bug (Microsoft Dynamics 365 v8.2.1.176)
This bug happens when a user double click to selects a readonly field on a Form, presses Ctrl+C and Ctrl+V in another/same application.
This bug however does not happen when you slowly select from left to right or right to left, which nobody uses anyway.
For example, we have a field on the **Account** form called `accountnumber` which has been changed to read-only.
If the value is ACC001 and a user double click to select it, copies it, it is copied to the clipboard as 2x values, ACC001ACC001
When we inspect the element with Chrome Developer Tools, we can see that Microsoft is using two fields to show the value, both of which is visible.

Modify the file `Microsoft Dynamics CRM\CRMWeb\_forms\controls\controls.css.aspx` so that the class .ms-crm-div-NotVisible has a display of none value.

```css
.ms-crm-div-NotVisible {
    position: absolute;
    <% if (CrmStyles.IsRightToLeft) {
        %>right: -10000px;
        <%
    }
    else {
        %>left: -10000px;
        <%
    }
    %>top:auto;
    width:1px;
    height:1px;
    overflow:hidden;
    **display:none;**
}
```
