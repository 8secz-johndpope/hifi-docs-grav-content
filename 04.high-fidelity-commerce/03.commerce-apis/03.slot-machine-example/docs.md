---
title: "Slot Machine Example"
taxonomy:
    category:
         docs
---

Say you want to create a slot machine game that pays out HFC in High Fidelity.

To do that:

# 1. Place an unscripted "Slot Machine" entity in your domain
First, you'll want to create a Slot Machine entity in your domain. This is the first step of the Slot Machine project, and is the most open-ended! It's up to you to decide what a "slot machine" looks like.

If you're following along with the particular example in this document, import the following JSON into your domain: [basicSlotMachine_noScripts.json](./basicSlotMachine_noScripts.json)

# 2. Pre-authorize Game Winning Payout Funds
In our slot machine example, players will pay you (the domain owner) 1 HFC to start playing, and the slot machine will pay out 25 HFC if/when the payer wins.

You will now learn how to pre-authorize the first payout of 25 HFC.

1. In your HUD or tablet, open the "INVENTORY" app.
2. Click the "Authorized Script" button.
3. Next to "Amount", enter "25".
4. Under "Optional Public Message", enter "Slot Machine Winnings".
    ![](./images/preAuth01.png)
5. Click "SUBMIT".
6. On the "Payment Authorized" screen, copy and paste the "Authorization ID" text string to a text file on your computer. You'll use this later.
7. On the "Payment Authorized" screen, copy and paste the "Coupon ID" text string to a text file on your computer. You'll use this later.
8. Click "CLOSE", then "I'M ALL SET".

# 3. Add the pre-authorized credentials to a payout database
You'll need to put the "Authorization ID" and "Coupon ID" secrets into some sort of database. Here's why:

Later, you are going to write an Assignment Client script (AC script) for the Slot Machine game logic, including payout logic. When the Slot Machine pays out, it needs to know about Authorization IDs and Coupon IDs associated with your pre-authorized payout funds.

There are many ways that the AC script can know about those secrets. In this example, we will use a Google Sheet to store the data, and a Google Script to access the data in the Sheet.

## First, create the Google Sheet:

1. Log into [Google Sheets](https://docs.google.com/spreadsheets/u/0/), and create a new spreadsheet. Give it whatever filename you want, such as "Slot Machine Payouts".
2. Name the current sheet "Authorizations" using the arrow on the tab at the bottom left of the screen.
3. Give the header row (the first row) the following labels in this order:
    * Used
    * HFC
    * Authorization ID
    * Coupon ID
4. In the second row, under the HFC column, put `25`.
5. In the second row, under the "Authorization ID" column, paste the Authorization ID that you saved from above.
6. In the second row, under the "Coupon ID" column, paste the Coupon ID that you saved from above.

## Second, create the Google Script

1. At the top of the Google Sheets window, click "Tools" -> "Script editor".
2. Name your currently-untitled project "Slot Machine Authorization Handler".
3. Copy and paste the contents of [this example GS script](./slotMachineAuthHandler.gs.txt) into the Script Editor.
4. Modify `var SPREADHSEET_ID` to match the Spreadsheet ID of your spreadsheet above
    * The Spreadsheet ID is embedded in the URL of the Google Sheets page
5. Save the script, using whatever filename you wish.
6. Click "Publish", then "Deploy as Web App..."
7. Follow Google's instructions to deploy your script as a web app. **Make sure** you set "Who has access to the app" to "Anyone, even anonymous". When finished, copy the URL you're given at the end of the process and save it somewhere you'll remember for later.
    * The web app URL will look something like `https://script.google.com/macros/s/ABCDEFGHIJKLMNOP_QRSTUVWXYZ1984373/exec`

**Make sure you keep the web app URL and the Google Sheet URL private**, since your authorization data will be visible to anyone with access to the sheet!

# 4. Allow users to "Add Credits" to the Slot Machine
You'll need to provide your users with a way to accrue slot machine play credits. You will do this by adding an Entity Script to two parts of the Slot Machine entity.

First, we need to write the Entity Script. This script will do the following:
* When a user clicks the text OR the border around the text, they will be prompted to pay a specified username (you) 1 HFC with the message "1 Slot Machine Play Credit".

[Click here](./addCreditsButton.js) to download a pre-made "Add Credits" entity script. Follow along with the comments in the code to understand what it's doing!

Be mindful of the fact that all users who load the Add Credits entities will be individually running this script as if it were a client script.

Next, add the entity script from above to the "Add Credits" text entity AND its parent:
1. Modify the `DESTINATION_USERNAME` variable within `addCreditsButton.js` to match your username.
2. Upload the `addCreditsButton.js` script to a publicly-accessible location such as S3. Copy its URL.
3. In Interface, use the `CREATE` app to select the "Click Here to Add Credits" text entity on the Slot Machine entity.
4. In the entity's Properties tab, scroll down to "Script" and paste the URL from above into the text box. Press Enter.
5. Lock the entity so nobody can change its attributes.
6. In Interface, use the `CREATE` app to select the border entity around the "Click Here to Add Credits" button on the Slot Machine entity.
7. In the entity's Properties tab, scroll down to "Script" and paste the URL from above into the text box. Press Enter.
8. Lock the entity so nobody can change its attributes.

# 5. Allow users to start the Slot Machine's reels
You'll need to provide your users with a way to start the slot machine's reels.

First, you'll have to write an Entity Script to put on the slot machine's Spin Lever. This script will send a message to an Assignment Client script that we'll write in a later step. This message will kick off the rest of the game logic.

[Click here](./slotMachineSpinLever.js) to download a pre-made "Spin Lever" entity script. Follow along with the comments in the code to understand what it's doing!

Be mindful of the fact that all users who load the Spin Lever entity will be individually running this script as if it were a client script.

Next, add the entity script from above to the "Spin Lever" entity:
1. Upload the `slotMachineSpinLever.js` script to a publicly-accessible location such as S3. Copy its URL.
2. In Interface, use the `CREATE` app to select the red Spin Lever sphere entity on the Slot Machine entity.
3. In the entity's Properties tab, scroll down to "Script" and paste the URL from above into the text box. Press Enter.
4. Lock the entity so nobody can change its attributes.

# 6. Obtain Auth Token
You'll have to obtain a High Fidelity authentication token that has the `commerce` scope. You will use this token in a later step when writing an Assignment Client script that'll check your Recent Economic Activity for recent transactions of 1 HFC made in your domain with a specific memo ("1 Slot Machine Play Credit").

To obtain this auth token:
1. Go to https://highfidelity.com/user/tokens/new
2. Name the token something memorable.
3. Select the `commerce` scope.
4. Click "Create Token".
5. Copy the token somewhere safe for now.

# 7. Write an authenticated Assignment Client Script
Next, you'll have to write an Assignment Client Script that will handle the slot machine game logic, including:
* Knowing when to start a new spin
* Knowing whether a user who attempted to spin has paid
* Changing the slot machine reel colors during a spin
* Checking the end state of the reels to determine win/loss
* Paying out pre-authorized funds

[Click here](./slotMachineACScript.js) to download a pre-made "Slot Machine" Entity Server Script. This script is quite long and is arguably the most important element of this project! Follow along with the comments in the code to understand what it's doing.

# 8. Run the Assignment Client script on your domain

## Running an AC Script from S3
**NOTE** that this method is **not fully secure**, since the S3 URL at which you host the script will be publicly accessible. This means your authentication token will be visible to anyone who guesses or has access to that S3 URL.

To run the above AC script on your domain from S3:
1. Modify your `slotMachineACScript.js` as follows:
    1. Set `HIFI_COMMERCE_TOKEN` to your HiFi commerce token from Step 4.
    2. Set `SLOT_MACHINE_REEL_1_ID` to the Entity ID of the leftmost slot machine reel.
    3. Set `SLOT_MACHINE_REEL_2_ID` to the Entity ID of the middle slot machine reel.
    4. Set `SLOT_MACHINE_REEL_3_ID` to the Entity ID of the rightmost slot machine reel.
    5. Set `SLOT_MACHINE_PLAY_TEXT_ID` to the Entity ID of the "Play Text" text entity right below the slot machine reels.
    6. Set `GOOGLE_SHEET_AUTH_SCRIPT` to the URL of the Google Script Web App from Step 3 above.
    7. Set `SLOT_MACHINE_AREA` to the coordinates around which the slot machine entity will be placed.
        * See the comments in the code for more details about why this is necessary.
2. Upload your `slotMachineACScript.js` script to a publicly-accessible location such as S3. Copy its URL.
3. Navigate to the Domain Settings page of your domain (for a local sandbox, this is probably http://localhost:40100/)
4. Click "Content" at the top of the page, then scroll to the "Scripts" section.
5. Under "Persistent Scripts", click the `+` button on the right column
6. Under "Script URL", paste the S3 URL from above.
7. Click "Save and restart" at the top right of the page

# Conclusion and Future Work
**You're done!** You should now have a basic but fully working slot machine in your domain. Remember that you cannot redeem your own pre-authorized transactions, so you will not be able to play your own slot machine. If you want to test the slot machine, sign into another account.

Here's a bunch of other ideas for extending the basic functionality of this slot machine:
* Lights and sounds corresponding to game state
* Spinning reels
* More secure game logic that prevents tampering
    * **NOTE** that, in this example, a user could modify the colors of the unlocked reels to match just before the game ends, and thus force a payout. This example does not cover anti-cheat or anti-tampering methods for securing your slot machine or funds!
* Moving spin handle
* "Credits" display using Overlays to show credits available to player
* More secure access to your AC script and pre-authorized transaction secrets (for example, using a local HTTP server)
* Payout different amounts based on variable pay-in amounts
* Payout with certified Marketplace items instead of HFC
* You could use a database of credits instead of relying solely on Recent Economic Activity. If you just use Recent Economic Activity, then, if the AC script restarts, users may gain credits they shouldn't.
* Better user messaging: The simple status messages coded in the example that are visible to slot machine players are nice, but not great or descriptive. It might be nice to have a way of contacting an admin if there's a problem.
* Multiple slot machines in one domain.