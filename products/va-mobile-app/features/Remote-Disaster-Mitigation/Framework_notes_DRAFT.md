This file will likely be absorbed into a process file (or somewhere else describing the functionality) in the future.

Notes on how the availability framework works, that future folks making decisions on turning one on may want to know:
* Address (Contact Info) and Phone Number (Contact Info) are collective sets - turning on the waygate for address applies to both home and mailing address, and phone number impacts home, work and cell.
* The login waygate will only ever function as use case 3 (the buttons to log in are available, and an informational alert appears onscreen)
* Similarly, screens that do not have use case 1 functionality (attempting to set up UC1 on these screens will result in nothing showing up; must be set up as either UC2 or 3) are: developer screen, home screen, all category screens, and the veterans crisis line.
* We do not have the ability to add waygates to webviews (such as the privacy policy, VA location finder, VA COVID-19 updates, etc)
* In "Personal Information", the "How to update or fix an error in your legal name" and "How to fix an error in your date of birth" large panels are the same screen, so any AF alerts for either of those will be applied to both
* WG_Claims doesn't exist as a toggleable screen as it is just a navigation holding screen with no external requirements and including it compounding testing and nav logic requirements unnecessarily.
* We get use case 2 and 3 "for free" whenever a new feature is created, as long as it uses the screen templates (aka no additional code needed, and we can use AF for those use cases with firebase and it works appropriately - tested during initial implementation). We do *not* get use case 1 for free, we always need to remember & make code changes to get that functionality.
* Although we do generally block the underlying API calls for screens that are hidden behind AF banners (so the analytics/activity logs match the , there are some exceptions: v0/user/contact-info when navigating away from the Letters Overview screen (ticket #7522), v0/military-service-history on the Home Screen, and the prescriptions list vo/health/rx/prescriptions?.... on the Prescription History screen

In order to get availability framework banners working in production, we need to put JSON into firebase. We've added some guardrails, but malformed JSON (ex: trailing commas) will cause crashes in the app. As a best practice, copy-paste from the known-to-work JSON below, and immediately double-check the functionality in the app, when setting this for a screen that's in production.

NB: for the 'fixed' versions of these, you will also need to work with a front-end engineer to add version information (ex: only show for app version X.XX and below) to the banner. Samples not provided for those.

Use Case 1 (deny access):

`{
  "enabled": false,
  "errorMsgTitle": "The app isn't working right now",
  "errorMsgBody": "While we fix the problem, you can still get your VA health and benefits information on VA.gov.",
  "type": "DenyAccess"
}`

Use Case 2 (deny content), not yet fixed:

`{
  "enabled": false,
  "errorMsgTitle": "We found a problem",
  "errorMsgBody": "We're sorry. We're fixing a problem we found [with/in this thing]. If you need help now with [the thing], call us.",
  "type": "DenyContent",
  "appUpdateButton": false
}`

Use Case 2 (deny content), with fix released:

`{
  "enabled": false,
  "errorMsgTitle": "You need to update the app",
  "errorMsgBody": "We fixed a problem [with/in this thing]. But to use this tool again, you need to update the app. If you need help now with [the thing], call us.",
  "type": "DenyContent",
  "appUpdateButton": true
}`

Use Case 3 (allow content and function), not yet fixed:

`{
  "enabled": false,
  "errorMsgTitle": "You may have trouble with [explain the thing]",
  "errorMsgBody": "We're fixing a problem [with/in this thing] that's affecting some Veterans. If you can't use [the thing] and need help now, call us.",
  "type": "AllowFunction",
  "appUpdateButton": false
}`

Use Case 3 (allow content and function), with fix released:

`	
{
  "enabled": false,
  "errorMsgTitle": "You may need to update the app",
  "errorMsgBody": "We've fixed a problem some Veterans were having [with/in this thing]. If you're still having trouble using this tool, you may need to update the app. If you need help now with [the thing], call us.",
  "type": "AllowFunction",
  "appUpdateButton": true
}`
