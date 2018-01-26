This document is to establish some consistent norms that will be used throughout the application. Using consistent language and decoration reduces cognitive load for the users and accelerates on-boarding.

# Iconography
Buttons and UI components should use supplemental icons (in addition to text) wherever possible and appropriate. When possible, reinforce existing choices (ie. the *Create* actions prepend the `plus` icon; if you add a *Create* button, yours should too)

# Table Rows
Table rows that offer contextual actions for each row should have an action buttons column, set to the far-right. 

## Action Buttons
All buttons should be classed with `btn btn-xs`, additional classes depend on the type of button. 

* *View* for buttons that lead to the `show` action for that row; the button should be additionally classed with `btn-primary`
* *Edit* for buttons that lead to the `edit` action for that row; the button should be additionally classed with `btn-info`, and the text pre-pended with the `edit` font-awesome icon.
* *Print* for buttons that link to a printing action for that row; the button should be additionally classed with `btn-info`, and the text pre-pended with the `print` font-awesome icon.
* *Delete* for buttons that link to the 'destroy' action for that row; the button should be additional classed with `btn-danger`, and the text pre-pended with the `trash` font-awesome icon.

Any other actions not covered above should follow these guidelines:

* No Action Button except *View* should be given `btn-primary`
* If the action does not directly modify data, class it with `btn-info`
* If the action directly modifies data or is destructive, class it with `btn-danger`

Use the FontAwesome icon that suits the action (use your judgement) and is not used for a different action.

# Component Styling
# Form Layout