---
title: "Paid Entry Example"
taxonomy:
    category:
         docs
---

Say you want to automatically give a user access to a part of your domain _if and only if_ a user has paid you 10 HFC in the current server session.

To do that:

# 1. Place a "VIP Zone" in your domain
Use the `CREATE` app to place a zone in your domain. This zone will be the designated "VIP Zone". Only users who have paid you 10 HFC in the current server session will be allowed to enter this zone. In other words, if the server restarts, users will have to re-pay for VIP status.

Move the VIP Zone to where you want it now.

# 2. Write an entity script for the "VIP Zone"
We need to write a script to put on the VIP Zone from Step 2. This script will do the following:
* When a user enters the zone, the script will check if they are a "VIP" OR if they are a domain admin and will remove them from the zone if they aren't.
* When the script receives a message from an AC script saying that someone has become a new paid VIP, and that new username is the avatar's username, the script will change their status to "VIP".

[Click here](./vipZoneEntityScript.js) to download a pre-made "VIP Zone" entity script. Follow along with the comments in the code to understand what it's doing!

Be mindful of the fact that all users who load the VIP Zone entity will be individually running this script as if it were a client script.

# 3. Add the entity script to the VIP Zone
You'll have to add the entity script from above to the VIP zone:
1. Upload the `vipZoneEntityScript.js` script to a publicly-accessible location such as S3. Copy its URL.
2. In Interface, use the `CREATE` app to select the zone from earlier.
3. In the entity's Properties tab, scroll down to "Script" and paste the URL from above into the text box. Press Enter.
4. Lock the zone entity so nobody can change its attributes.

# 4. Obtain Auth Token
You'll have to obtain a High Fidelity authentication token that has the `commerce` scope. You will use this token in a later step when writing an Assignment Client script that'll check your Recent Economic Activity for recent transactions of 10 HFC made in your domain.

To obtain this auth token:
    a. Go to https://highfidelity.com/user/tokens/new
    b. Name the token something memorable.
    c. Select the `commerce` scope.
    d. Click "Create Token".
    e. Copy the token somewhere safe for now.

# 5. Write an authenticated Assignment Client script
Now, you'll write an Assignment Client script containing the authentication token from above that checks your Recent Economic Activity for recent transactions of 10 HFC made in your domain.

[Click here](./vipZoneACScript.js) to download a pre-made "VIP Zone" entity script. Follow along with the comments in the code to understand what it's doing!

# 6. Run the Assignment Client script on your domain
To run the above AC script on your domain:
1. Navigate to the Domain Settings page of your domain (for a local sandbox, this is probably http://localhost:40100/)
2. Click "Assignment" at the top of the page.
3. Paste in the script from (2) above, **making sure that you set `HIFI_COMMERCE_TOKEN` to your token from Step 4!**
4. Click "Run" at the top right of the page.

# 7. (Optional) Make it easy for others to pay you 10 HFC
This step will teach you how to create an entity that, when clicked, will prompt the user who clicked it to pay you 10 HFC for VIP status. This will use the "Pay-In Commerce API".

This step is optional because it doesn't matter _how_ a user sends you 10 HFC to earn VIP status, so you don't _have_ to use this method. For example, if a user used the `Inventory -> Send Money -> Nearby` interface to send you 10 HFC while you were in your domain, they would still get "VIP status".

## 7a. Place a "Tip Cube" in your domain
Use the `CREATE` app to place a cube in your domain. This cube will be the designated "Tip Cube". Users who click on it will be prompted to pay you 10 HFC. Move the Tip Cube to where you want it now.

## 7b. Write an entity script for the "Tip Cube"
We need to write a script to put on the Tip Cube from Step 7a. This script will do the following:
* When a user clicks the cube, they will be prompted to pay a specified username (you) 10 HFC with the message "10 HFC for VIP status!".

[Click here](./tipCube.js) to download a pre-made "Tip Cube" entity script. Follow along with the comments in the code to understand what it's doing!

# 7c. Add the entity script to the Tip Cube
You'll have to add the entity script from above to the Tip Cube entity:
1. Modify the `DESTINATION_USERNAME` variable within `tipCube.js` to match your username.
2. Upload the `tipCube.js` script to a publicly-accessible location such as S3. Copy its URL.
3. In Interface, use the `CREATE` app to select the Tip Cube from earlier.
4. In the entity's Properties tab, scroll down to "Script" and paste the URL from above into the text box. Press Enter.
5. Lock the entity so nobody can change its attributes.

# You're done!
You should now have a working "VIP Zone" in your domain that only VIPs can enter. You should now also have a "Tip Cube" in your domain that'll enable people to quickly become VIPs by paying you the required amount of HFC.