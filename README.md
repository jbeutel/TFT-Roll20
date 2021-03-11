# TFT-Roll20
 Roll20 APIs & Character Sheet for TFT
v0.1
Fixed some character sheet functions and added some attributes to streamline the connection between tokens and character sheets. Still needs some work, and currently all attributes are displayed when some should be hidden in the final product.
CombatMaster TFT is my slight edit of another script (credits at top of script) to make it work with TFT's DX turn order. I wish to modify this further to add phases and TFT-specific conditions and turn-counting effects, but that's a lot of work and a lot of it is pretty easy to do manually with the tools provided (such as having a "Movement phase" character token with a very high DX to happen at the top of each round).
Attack script is my large edit of a basic script to make it work with not just TFT but apply damage to character sheets properly. It has room for expansion to add in some automated modifiers (perhaps range) and checks for weapon types and enemy equipped armors and kinds of armor, but for now most of this is done at the character sheet level (listing total block vs. block w/o shields, moving a weapon to the top of the weapon list to use it) or manually (counting range modifiers, adding MW bonuses, etc).
I recommend trying Duplicate a Character and Token (it's a selectable API, not a new script). It doesn't quite work on my end (it creates duplicate character sheets: Wolf 1, Wolf 2..., but the duplicated tokens are broken). It still streamlines things by making a series of character sheets that are easily selected, so you !DupCharToken x and then copy paste the original token X time and then tell each one to be tied to character sheet n in their drop down, which is pretty fast once you're used to it.

Setup:

	-Go into Game Settings for your campaign. Switch to a custom character sheet. Copy paste character-sheet into the HTML section, and css-styling into the CSS Styling section.

	-Go to APIs and add a new script, copy paste the attack script in.

	-Save changes and add another new script with the CombatMasterTFT script. 

Recommended Macros (you'll make these yourself, and you can make your own variations if you prefer):

	- name: start-combat, content: (!cmaster --main), settings: (show in bar) //click this to start the combatmaster API.

	- name: attack-basic, content: (!attack @{selected|token_id} @{target|token_id} [[3d6]] 0 [[ @{selected|repeating_weapons_$0_weapondamage} ]] @{selected|repeating_weapons_$0_weaponname} 1), settings: (show as token action, visible to all players) //Token does the most default attack possible.
	
	- name: attack-fancy, content: (!attack @{selected|token_id} @{target|token_id} [[ ?{Attack Dice?|3d6} ]] ?{To Hit Modifier?|0} [[ ?{Damage Dice?|@{selected|repeating_weapons_$0_weapondamage}} ]] ?{Weapon?|@{selected|repeating_weapons_$0_weaponname}} ?{Flank 0=y, 1=n?|1} ), settings: (show as token action, visible to all players) //Token does the most customizable attack possible
	
	- name: check-weapon, content: (@{selected|repeating_weapons_$0_weaponname} @{selected|repeating_weapons_$0_weapondamage}), settings: (show in bar) //Checks the top weapon of the token's character sheet's weapon list - ie. the ready weapon (or in special cases you may need to switch in a left hand weapon or unarmed attack damage or so on).
	
	- name: initiative, content: (Players: [[1d6]], [[1d6]], [[1d6]] GM: [[1d6]], [[1d6]], [[1d6]]), settings: (show in bar) //meant to just speed up rolling initiative at the top of a round. Click it and you'll see two sides (GM, Players) roll 3 dice, read each from left to right until one wins.

Using:

	-When making a token, make it represent a character sheet. Then, tell it to use dmg (USE AS BAR3), total block, and ma as its bars. Finally go to the character sheet and tell it to use this counter (you need to do it both ways so that you can drag and drop the character sheet onto the map).
	
	-Those are just my preferences, but it should show the token with adjMA/MA, shieldless hits blocked/total hits blocked (to reference for flanking), and remaining ST/ST. Note this is 'dmg' and NOT ST, preserving 'adjST' for Aid spells and so on. Fatigue and Hits are counted equally as part of dmg.
	
	-When you're ready to start combat, click on start-combat (or start cmaster in another way), drag select all combatant tokens, and click the play button in the menu that has appeared in the chat menu. You can configure the details, but at the very least this should create a turn order listing based on DX.
	
	-You can click through the character turns. There are currently no built in "phases," so it's just the action phase listed in adjDX order. At any time, a token can be moved by the controling players (or GM), and at any time a selected token should have 3 buttons along the top of the screen: attack-basic, attack-fancy, and check-weapon.
	
	-When one of these buttons is pushed, select a target (be careful to have tokens not overlapping, you don't want to select the wrong one). Basic attacks will then be carried out (3/DX, no modifiers, with the weapon listed at the top of the character sheet's weapons, no damage modifiers, to the front armor of the opponent). Fancy attacks will instead request input for each of these, but will default to the basic (so you can just hit enter for fields you don't wish to modify). 
	
	-Attacks should automatically be parsed, with successful hits rolling damage and subtracting hits blocked and applying this as Hits on the character sheet which should pass through as damage to the character's bar values. As of now this is also assumed to be bar3, which needs to be edited out for v0.2 as it is redundant with the Hits applied anyway and can only cause problems.
	
	-You'll get an in-chat report of the attack, with varying detail depending on the success (a miss gives no information, hits give information on armor, and some hits tell you to roll for Crippling Hits as in the optional rules).
	
	-You'll have to manually handle knock down, -2 DX from damage, forced retreats, and so on. You can use the CombatMaster conditions list to make this a bit faster though still manual - I suggest reading their instructions for how to adjust settings. Spells are not well implemented yet (mostly manual) though you can use an attack-fancy for the attack portion of missile spells. 
	
	-Note that as it stands you cannot copy paste tokens without breaking things. The character sheet doesn't get duplicated and you end up with two tokens affecting one sheet. If you want, say, multiple Wolves, you'll have to make multiple character sheets and tie them to tokens separately. There are scripts out there that say they streamline this but the ones I've tried so far do not work. An alternative would be to use tokens that are decoupled from a character sheet and reworking some things to use bar values only, at least for tokens that say they aren't from a character sheet.
	
	-General reminder, E+mousewheel to rotate facing, shift + double left click to open a character sheet. As is, we assume a degree of active interaction with the character sheet for things like selecting a weapon to use (though this can be altered manually with attack-fancy).

Objectives for the future:

	- Add character sheet rolls to cast spells that automatically subtract Fatigue.
	
	- Add spell tracker display that can be checked to either turn off or pay for continuing spells. Perhaps should be at the character sheet level.
	
	- Automatic Knock Down/-2DX/-3DX checker for damage, possibly also a duration checker that automatically removes them after a certain amount of time. Could be 'reminders' instead if that works better.
	
	- Tidy up the character sheet rollers. Right now they just roll 3d6, it would be nicer if they could roll variable d6 and explicitly compare them to whatever stats are being rolled.
	
	- Count hits blocked by Equipped, Shield, MG, Armor, Magic, automatically (attributes are there, just need to add a sheetworker).

Potential Objectives:

	- Need to be careful to not automate to the point where one needs to carefully dig through the weeds to handle special cases (i.e. a monster that can't be knocked down). As such probably will never add something that restricts movement to a movement phase, or only allows one action per turn.

	- Range modifiers, attacks that distinguish between 1- or 2-handed melee/thrown/missile/armor piercing and properly apply shields, MG, armor, magical protection

	- Automatically accounting for MW, Thrown Weapons Talents.

	- Sheetworker that automatically makes an unarmed combat attack damage. Perhaps also automatically creates the special attacks and bonuse damage for Unarmed Combat talents.

	- Some sort of HTH handler?

	- Ammo tracking

	- Weight tracking/inventory beyond armor+weapons.

	- Make switching weapons a little easier?
	
	- Always looking for more ideas!

If SJ allows...

	- Would be super cool to get the basic critters and weapon list into the library to streamline summoning creatures and picking weapons.