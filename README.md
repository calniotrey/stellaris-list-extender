# Stellaris List EXtender  
This mod is a utility mod. It adds functions to allow modders to use lists of variables or event_targets and to sort them. It also allows modders to get the value of a trigger condition (such as income, or amount of minerals in the reserve) stored into a variable. See "Examples" for some examples of what you can do.  
  
## Requirements
None  
  
## Compatibility
No compatility problem
Not iron-man compatible
  
## How to use this in your mods
1. Set this mod ([Steam workshop mod](https://steamcommunity.com/sharedfiles/filedetails/?id=2054717582)) as a dependancy in the steam workshop
2. Done !
  
If you want to upload your mod outside of the steam workshop, feel free to also upload this mod as is to the same website (separately). Only put a link to this repository on the mod page/description and contact me so that I can update this readme with the uploaded SLEX mod link. Please do not upload this mod to a website already having it and do not include this mod in yours.  

## How to test latest version of this mod
1. Open the launcher from stellaris.
2. Go to mods > Mod tools
3. Click `Create Mod`
4. Fill in the blanks and click create
5. Go to the Stellaris mods folder (in my case `...\Documents\Paradox Interactive\Stellaris\mod\`).
6. Extract the content of the repository inside of the newly created mod folder
7. Activate the mod
  
## Error codes :
These codes appear in SLEX_error_code
Error Code | Description
------------ | -------------
0 | Everything is ok
1 | Index is not between 0 and 255 (=ff in hexadecimal) included
101 | The event_target doesn't exist (check the scope and the event chain) using constant index
102 | The event_target doesn't exist (check the scope and the event chain) using variable index
  
## Detailed description : Read before using
This mod allows modders to use lists of variables and lists of (non-global) event_targets.  
A list (both for variables and for event_targets) is characterized by its name (for example `resources_list`). A list of variables has the same scope rules as a variable. A list of event_targets has the same rules as a non-global event_target: it only lasts in the unbroken event chain.  
To access a certain index of the list, you can use `listname_index`. A list is basically just a collection of variables beginning with the list name followed by an underscore. However, the index **must** have the lowest amount of digits possible (so 0, 1, 2, ... 9, a, b, c, d, e, f, 10, ...) ie if strictly under 16 (decimal) = 10 (hexadecimal) then there is one digit, else there is 2.  
  
A quick note about a little known Stellaris modding limitation: scripted_effects (it may only be those having parameters) and probably triggers too, can only have a **maximum of 5 levels on the stack** (for 2.6.*, may have increased for 2.7.*). This means that if a scripted_effect named A calls B, B calls C, C calls D, D calls E, then any call from E to another scripted_effect F will make the game consider F as an unknown scripted_effect and will (probably) break the whole scripted_effect chain.  
Due to this limitation, the index has to be between 0 (included) and ff (included). This is in a hexadecimal base, meaning that f if 15 and ff is 255.  
  
The highest index (ff) is defined by the number of stack levels SLEX uses to get and set the variables/event_targets. The getter and setter (and all similar functions such as change/multiply/...) use 1 stack level for constant index and 2 stack levels for variable index. The sort function uses 3 stack levels. This means that when writing your scripted_effects, you should have a maximum of 2 stack levels when using the sort functions, 3 when using getter/setter with a variable index and 4 when using a constant index.  
  
Now about getting the value of a trigger condition into a variable. This is also a scripted effect and it currently uses **2 levels of the stack**. It can take nearly any trigger condition as parameter and store its value inside a variable. There is one big limitation however: it will always truncate the value to `x * (16^exponent)` where x in the range `[-255:255]` and exponent a positive integer (by default 0). You can set the exponent by using the ROUNDING_ZEROES parameter with as much "0" as exponent (if 2, then "00", if 0 then omit it).  
This means the resolution will always be `16^exponent` (by default, 1). If the real value would lead to an `x` out of bounds, then it will be considered as +/-255.  
The decimal part of the value is discarded (i.e. the value is truncated).  
  
There is also 2 scripted_effects (for "normal" and advanced trigger conditions) for getting decimal places. One for getting values in `[-15.9:15.9]` with a resolution of 0.1, and one for values in `[-1:1]` with resolution of 0.01.  
This scripted_effects were originally implemented by Diagrapher.  
  
Note : The reason for this effect to be so limited is that Stellaris compiles the scripted_effects using parameters at the launch of the game. This means that every occurence of this scripted_effect produces around 30 (as there is `2*16 + 1` effects due to the negative and positive). This causes a RAM usage increase. You can (approximately) write 1 000 times this scripted_effect with a RAM increase of around 2Gbytes. Writing 2 000 times this scripted_effect might lead to consequences for the players with low RAM amount such as infinite loading time or CTD. If the effect had a higher range, then this would mean a smaller possible amount of times it could be written in the code.  
Please note that this numbers are the number of occurences in code, not during the game. If you loop one occurrence one million times, there won't be any problems. If you write one million times this scripted_effect, there will be.  
  
There is in fact 2 different scripted_effects (2 for integer, 2 for decimal):
* First one is for getting "simple" trigger condition such as income : anything that is of the form `CONDITION_NAME < value` where value is an integer/float. For income, use "income" for CONDITION_NAME  
* Second one is for getting advanced trigger condition such as the minerals reserve : anything that is of the form `CONDITION_NAME = { FIRST_KW = SECOND_KW THIRD_KW < value }` or `CONDITION_NAME = { FIRST_KW < value }` (in that case don't use SECOND_KW and THIRD_KW) where value is an integer/float. For minerals reserve, use "has_resource" for CONDITION_NAME, "type" for FIRST_KW, "minerals" for SECOND_KW and "amount" for THIRD_KW. That's because you usually check the minerals reserve with : `has_resource = { type = minerals amount < 500 }`  
  
One last warning before closing the "Must read" part: For some reason, Stellaris doesn't like 1 letter variable. So please do not use i/a/b/c/...  
  
## Examples
To access the ingame examples, open the console and type `effect set_global_flag = SLEX_example`. This will unlock the (english only localized) example edicts. When active, you have a 10 days delay to get into observer mode (if you need/want to) before the event fires. These examples have the purpose to display the possibilies as well as allow to do a quick performance check.  
  
List of the ingame examples:  
* SLEX_give_pops_by_ranking: It ranks the empires by descending number of pops and give 1 pop per distance in the ranking to the first. That means the empire with the most amount of pops gets 0 pops, the second gets 1 pop, and so on. Only default empires as well as (awakaned) fallen empires are taken into account. These empires also need to have a capital_scope (which they probably have).  
* SLEX_sort_twice: Fills a list fully (256 elements) in descending order, then sorts it in ascending order and then in descending order. You can use this to see the sort performance in the worst case.  
* SLEX_fill: Fills a list fully (256 elements) in descending order. Use this before the following 2 edicts.  
* SLEX_sort_filled_20_times: Sorts the list filled by SLEX_fill 20 times (alternatively ascending then descending). Use this to estimate the sort performance independantly from the fill.  
* SLEX_sort_filled_200_times: Sorts the list filled by SLEX_fill 200 times (alternatively ascending then descending). Use this to estimate the sort performance independantly from the fill if the previous one was to fast.  
* SLEX_get_custom_parameter_1_times: Gets the income (energy production before consumption) and stores it in a variable. Please wait for the second month of the game before activating this, or you won't have any income yet.  
* SLEX_get_custom_parameter_1_000_times: Like above, but 1 000 times for performance check.  
* SLEX_get_custom_parameter_1_000_000_times: Like above, but 1 000 000 times for performance check.  
* SLEX_get_custom_parameter_advanced_1_times: Gets the minerals currently held in reserve and stores it in a variable.  
* SLEX_get_custom_parameter_advanced_1_000_times: Like above, but 1 000 times for performance check.  
* SLEX_get_custom_parameter_advanced_1_000_000_times: Like above, but 1 000 000 times for performance check.  
* SLEX_get_custom_parameter_decimal : Gets the used naval capacity stored into a variable. Resolution of 0.1 and max value of +/- 15.9.  
* SLEX_get_custom_parameter_advanced_decimal : Gets the influence income stored into a variable. Resolution of 0.1 and max value of +/- 15.9.  
* SLEX_get_custom_parameter_percentile : Gets the used naval capacity stored into a variable. Resolution of 0.01 and max value of +/- 1.  
* SLEX_get_custom_parameter_advanced_percentile : Gets the used naval influence income stored into a variable. Resolution of 0.01 and max value of +/- 1.  
  
On my computer, at the start of the game (game running on very fast, medium size galaxy with 8 AI), a full (256 elements) sort takes around 150 ms. Take in mind this is a non-optimized non-adapted bubble sort so complexity in O(length^2).  
Getting a (non-advanced) custom parameter (like the income) takes around 10^-6 seconds.  
Getting an advanced custom parameter (like the mineral reserve) takes around 2*10^-6 seconds.  
  
## Documentation
The documentation is splitted in several .md files:
* [Documentation for lists](doc/SLEX_lists.md)
* [Documentation for get_parameter](doc/SLEX_get_parameter.md)
* [Documentation for utilities](doc/SLEX_utilities.md)
* [Documentation for auxiliaries](doc/SLEX_auxiliaries.md)
  
## Credits
Thanks to Diagrapher for the original implementation of _decimal and _percentile scripted_effects and for various advice.  
  
## Legal Stuff
You are not allowed to put this mod without any modification on any website that has already one upload of this mod without my approval. This is only so there is one version on it for this repository. If you want to upload it as is to a new website (for example paradox mods), you are welcome to do so and please contact me so that I can put the link in this README. If you modified this mod, then you may also upload the modified version wherever you want as long as you specify that it isn't the original. Forking in github is of course allowed even if you didn't make any changes yet.  
  
You are not allowed to include this mod as is in your own mods (or modpacks) without my approval. This is here so that players don't have several versions of this mod. This could cause issues and code duplication. Set this mod as a dependancy instead, so that players only have one up-to-date version of this mod.  
  
You are allowed to use the technical ideas behind this mod for your own mods. If you need to adapt this mod to your needs, you are also allowed to include modified version of this mod in your mods. In this case make sure to not have any conflict with this mod. This means that players having both version activated should not get a single error line from them. But please take in mind that doing this means not having directly any update from this mod which means no performance update for your players. I would be very happy to include code that you think necessary in this mod (make some pull request or just contact me if you aren't familiar with github).  
  
You are allowed to use this mod and the ideas behind it for any type of content that is outside of Stellaris modding.  
  
You are not allowed to use this mod or modified version of this mod for any content that has a paywall without my approval. Having a donation link is of course okay. Paradox Interactive has of course my approval.  

If you are using this mod, modified version of this mod or the technical concepts behind it, you have to put a link to this github repository or to the steam workshop mod. Having your mod list this mod as a dependancy via the steamworkshop is enough (no need for additionnal links in the description).  This is here so other modders can find this mod easily.  
  
As usual, I'm not responsable for any damage/incident resulting of the use of this mod, including (but not limited to) save corruption, achievements blocked and so on.
