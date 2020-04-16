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
  
A quick note about a little known Stellaris modding limitation: scripted_effects (and probably triggers, too) can only have a **maximum of 5 levels on the stack**. This means that if a scripted_effect named A calls B, B calls C, C calls D, D calls E, then any call from E to another scripted_effect F will make the game consider F as an unknown scripted_effect and will (probably) break the whole scripted_effect chain.  
Due to this limitation, the index has to be between 0 (included) and ff (included). This is in a hexadecimal base, meaning that f if 15 and ff is 255.  
  
The highest index (ff) is defined by the number of stack levels SLEX uses to get and set the variables/event_targets. The getter and setter (and all similar functions such as change/multiply/...) use 1 stack level for constant index and 2 stack levels for variable index. The sort function uses 3 stack levels. This means that when writing your scripted_effects, you should have a maximum of 2 stack levels when using the sort functions, 3 when using getter/setter with a variable index and 4 when using a constant index.  
  
Now about getting the value of a trigger condition into a variable. This is also a scripted effect, and it currently uses **4 levels of the stack**. It can take nearly any trigger condition as parameter and store its value inside a variable.  
The decimal part of the value is discarded (i.e. the value is truncated) for the default scripted_effect. If you want to have 2 decimal places after the "." (i.e. value is truncated to 0.01), then use the decimal version of the scripted_effect.  
On top of that for the integer (i.e. non-decimal) scripted_effect, if the real value exceeds 65 536 (2^16) included, the variable will hold +/- 65 535. For the decimal scripted_effect, the maximum value is +/-255.99.  
The decimal scripted_effect was implemented by diagrapher.  
  
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
* SLEX_get_custom_parameter_1_times: Gets the income (energy production before consumption) and stores it in a variable.  
* SLEX_get_custom_parameter_1_000_times: Like above, but 1 000 times for performance check.  
* SLEX_get_custom_parameter_1_000_000_times: Like above, but 1 000 000 times for performance check.  
* SLEX_get_custom_parameter_advanced_1_times: Gets the minerals currently held in reserve and stores it in a variable.  
* SLEX_get_custom_parameter_advanced_1_000_times: Like above, but 1 000 times for performance check.  
* SLEX_get_custom_parameter_advanced_1_000_000_times: Like above, but 1 000 000 times for performance check.  
  
On my computer, at the start of the game (game running on very fast, medium size galaxy with 8 AI), a full (256 elements) sort takes around 150 ms. Take in mind this is a non-optimized non-adapted bubble sort so complexity in O(length^2).  
Getting a (non-advanced) custom parameter (like the income) takes around 10^-6 seconds.  
Getting an advanced custom parameter (like the mineral reserve) takes around 2*10^-6 seconds.  
  
## Documentation
Now let's look into the detailed documentation. Let's put aside the promised sorting functions for a small moment to quickly look at the loading of trigger condition into variables.  
  
### Get custom parameter
As stated in the description above, there is 2 different scripted_effects depending on the nature of the trigger condition.  
  
#### Simple trigger condition
`SLEX_get_custom_parameter = { CONDITION_NAME = trigger_condition VARIABLE = variable_name }`  
* trigger_condition : the name of the trigger condition (string)
* variable_name : the name of the variable where to write the result (string)
  
Use this for something like income that could be checked directly with `CONDITION_NAME < value`, use it like this :  
`SLEX_get_custom_parameter = { CONDITION_NAME = income VARIABLE = output_income }`  
  
#### Advanced trigger condition
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = trigger_condition
    FIRST_KW = first_keyword
    SECOND_KW = second_keyword
    THIRD_KW = third_keyword
    VARIABLE = variable_name
}
```  
* trigger_condition : the name of the trigger condition (string)
* first_keyword : the name of the first keyword of the trigger condition (string)
* second_keyword : the name of the second keyword of the trigger condition (string) OPTIONNAL
* third_keyword : the name of the third keyword of the trigger condition (string) OPTIONNAL
* variable_name : the name of the variable where to write the result (string)
  
Use this for something like minerals reserve that could be checked directly with :  
```
CONDITION_NAME = {
    FIRST_KW = SECOND_KW
    THIRD_KW < value
}
```  
  
Use it like this :  
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = has_resource
    FIRST_KW = type
    SECOND_KW = minerals
    THIRD_KW = amount
    VARIABLE = variable_name
}
```  
  
You can also use this for something like species count (without limit) that could be checked directly with :  
```
CONDITION_NAME = {
    FIRST_KW < value
}
```  
  
In that case **DO NOT** use SECOND_KW and THIRD_KW, but use it like this :  
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = count_species
    FIRST_KW = count
    VARIABLE = variable_name
}
```  
  
### Sort
Now to the sort part.  
  
#### Sort of a list of variables
With SLEX, sorting a list of variables is pretty easy. Just use the `SLEX_sort_bubble` scripted_effect. Please remember to only use 2 levels of the stack when using it (reasons and explanations above). By default, the sort order is ascending. Use `ASCENDING = no` for descending order.  
  
Examples :  
`SLEX_sort_bubble = { LIST = listname }`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = yes } # same as above`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = no }`  
  
#### Sort of a list of event_targets zipped to a list of variables
What does that mean? Let's say you want to sort the empires by number of pops (like the example). You can count the number of pops easily (iterate with `every_owned_pop` for every country) and stock into a list. But once sorted, how can you say which country is first (except by recalculating the number of pops for every empire and checking every pair `(empire, value in the list)`).  
A good way to solve this problem is by saving the empires in a list of event_targets alongside the saving of their number of pops by using the same index. This means `pops_number_2` stores the number of pops of the empire stored in `targets_list_02`. We then say the list of events_target is *zipped* to the list of variables (even if there is no real link between the two outside of the modders code). Then you can't just sort the list of variables (`pops_number` in our example). Instead you sort the list of event_targets zipped with the list of variables. That means that after the zipped sort, `targets_list_2` will still have `pops_number_2` number of pops and that the list `pops_number` is sorted.  
  
There is however one limitation: there must not exist an index in the list where the zipped event_targets list doesn't have an event_target saved.  
  
Examples :  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname }`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname ASCENDING = yes } # same as above`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = list ASCENDING = no }`  
  
### Filling and reseting a list
Note : there is no concept of erasing event_targets, so there is no possibility in this mod to erase them.

#### Reseting a list
`SLEX_reset_list_variable = { LIST = listname }`
* LIST : the list name (string)
  
Just fills the list with 0 and resets its length to 0.
  
Example :  
`SLEX_reset_list_variable = { LIST = test_list }`  
  
#### Filling a list
`SLEX_fill_list_variable = { LIST = listname AMOUNT = amount VALUE = value}`
* LIST : the list name (string)
* AMOUNT : the value up to which (excluded) the list should be filled (either int/float, or a variable ie a string)
* VALUE : the value used to fill the list (either int/float, or a variable ie a string)
  
Just fills the list with VALUE from 0 to AMOUNT-1 : it sets its length to AMOUNT.  
  
Example :  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = 5 VALUE = 42 }`  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = 3 VALUE = my_variable }`  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = some_variable VALUE = my_variable }`  
  
#### Filling a zipped list
`SLEX_fill_list_zipped = { LIST = listname ZIPPED_LIST = zipped_list_name AMOUNT = amount VALUE = value TARGET = target }`
* LIST : the list name (string)
* ZIPPED_LIST : the name of the list of event_targets (string)
* AMOUNT : the value up to which (excluded) the list should be filled (either int/float, or a variable ie a string)
* VALUE : the value used to fill the list (either int/float, or a variable ie a string)
* TARGET : the event_target used to fill the zipped list (string). Defaults to `this`
  
Just fills the list with VALUE from 0 to AMOUNT-1 and the zipped_list with TARGET (defaults to `this`) : it sets the list length to AMOUNT.  
  
Example :  
`SLEX_fill_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev AMOUNT = 5 VALUE = 42 TARGET = event_target:ev_target }`  
`SLEX_fill_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev AMOUNT = 3 VALUE = my_variable }`  
`SLEX_fill_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev AMOUNT = some_variable VALUE = my_variable }`  
  
### Handling a list like a FILO (first in last out)
If you want to handle the list like a pile, you can!  
  
#### Append to a list
`SLEX_append_list_variable = { LIST = listname VALUE = value }`
* LIST : the list name (string)
* VALUE : the value appended to the list (either int/float, or a variable ie a string)
  
Appends VALUE to the end of the list and increments its length by one.  
  
Example :  
`SLEX_append_list_variable = { LIST = test_list VALUE = 42 }`  
`SLEX_append_list_variable = { LIST = test_list VALUE = my_var }`  
  
#### Append to a zipped list
`SLEX_append_list_zipped = { LIST = listname ZIPPED_LIST = zipped_list_name VALUE = value TARGET = target }`
* LIST : the list name (string)
* ZIPPED_LIST : the name of the list of event_targets (string)
* VALUE : the value appended to the list (either int/float, or a variable ie a string)
* TARGET : the event_target appended to the zipped list (string). Defaults to `this`
  
Appends VALUE to the end of the list and TARGET (defaults to `this`) at the end or the zipped list and increments its length by one.  
  
Example :  
`SLEX_append_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev VALUE = 42 TARGET = event_target:ev_target }`  
`SLEX_append_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev VALUE = my_variable }`  
  
#### Pop from a list
`SLEX_pop_list_variable = { LIST = listname VALUE = value }`
* LIST : the list name (string)
* VALUE : the variable where the popped value should be stored (string)
  
Pops the last element of the list (stores it to the VALUE variable and replace it by 0) and decrements its length by one. If the list was empty, does nothing instead.  
  
Example :  
`SLEX_pop_list_variable = { LIST = test_list VALUE = output_variable }`  
  
#### Pop from a zipped list
`SLEX_pop_list_zipped = { LIST = listname ZIPPED_LIST = zipped_list_name VALUE = value TARGET = target }`
* LIST : the list name (string)
* ZIPPED_LIST : the name of the list of event_targets (string)
* VALUE : the variable where the popped value should be stored (string). By default, does not store it anywhere.
* TARGET : the event_target where the popped event_target should be stored (string).
  
Stores the last element of the 2 lists, replaces the last element of the list of variables by 0 and decrements its length by one. If the list was empty, does nothing instead.  
As there is no possibility of erasing/deleting stored event_targets, you can't remove the popped event_target.  
  
Example :  
`SLEX_pop_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev VALUE = output_variable TARGET = event_target:ev_target }`  
`SLEX_pop_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev TARGET = event_target:ev_target }`  
  
### Accessing the stored variables/event_targets
You may ask "But I can't just write `listname_variable`! How can I access a certain index using a variable ?"  
And that's the core of the mod. Due to the same stellaris limitation, you can't just use a big switch on the index for performance reason (1 second freeze for an index between 1 and 128 on my machine). That's why the code uses an integar division (despite modulo not being available directly in stellaris) to convert a variable index into a string that can be used to get the variable. See `common/scriped_effects/SLEX_scripted_effects.txt` and `common/scriped_effects/SLEX_scripted_effects_auxiliary.txt` for more details.  
  
#### Using constant index
As seen, you can directly use the list name followed by an underscore followed by the index to access the variable. However, there are some scripted_effects that this mod adds. Please remember that you can't currently use `listname_0` but you have to use `listname_00`. This means that when using a constant index, make sure to always have a 2 digit number.  
  
##### SLEX_get_list_variable_const_index
`SLEX_get_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the variable where to write the listname_index (string)
  
Example :  
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_output }`  
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_output }`  
  
##### SLEX_set_list_variable_const_index
`SLEX_set_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_change_list_variable_const_index
`SLEX_change_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_subtract_list_variable_const_index
`SLEX_subtract_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_multiply_list_variable_const_index
`SLEX_multiply_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = 3 }`  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 1 VALUE = test_input }`  
  
##### SLEX_divide_list_variable_const_index
`SLEX_divide_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value by which listname_index should be divided (either int/float, or a variable ie a string)
  
Example :  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 17 VALUE = test_input }`  
  
##### SLEX_load_list_event_const_index
`SLEX_load_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the event_target name to be used when saving listname_index as an event_target (string)
  
This loads the `listname_index` and save it under the name `target` as an event_target. Remember that it is not a global event_target.  
  
Example :  
`SLEX_load_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = my_event_target }`  
  
##### SLEX_store_list_event_const_index
`SLEX_store_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the scope to be saved to listname_index as an event_target (string). Default value : this
  
This saves the `target` and save it under the name `listname_index`. If you want to save an existing event_target, remember to specify `event_target:` in front of its name (see third example). Remember that it is not a global event_target.  
  
Example :  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = this }`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 } # This is the same as above`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = event_target:my_event_target }`  
#### Using variable index
So this is really the core of the mod. The first functions are basically the same as for constant index, except now the INDEX has to be a variable (ie a string corresponding to the name of the variable).  
  
##### SLEX_get_list_variable_var_index
`SLEX_get_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the variable where to write the listname_index (string)
  
Example :  
`SLEX_get_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_output }`  
  
##### SLEX_set_list_variable_var_index
`SLEX_set_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_set_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_set_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_input }`  
  
##### SLEX_change_list_variable_var_index
`SLEX_change_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_change_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_change_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_add }`  
  
##### SLEX_subtract_list_variable_var_index
`SLEX_subtract_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_subtract_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_subtract_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_subtract }`  
  
##### SLEX_multiply_list_variable_var_index
`SLEX_multiply_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_multiply_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_multiply_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_multiply }`  
  
##### SLEX_divide_list_variable_var_index
`SLEX_divide_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value by which listname_index should be divided (either int/float, or a variable ie a string)
  
Example :  
`SLEX_divide_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_divide_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_divide }`  
  
##### SLEX_load_list_event_var_index
`SLEX_load_list_event_var_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* TARGET : the event_target name to be used when saving listname_index as an event_target (string)
  
This loads the `listname_index` and save it under the name `target` as an event_target. Remember that it is not a global event_target.  
  
Example :  
`SLEX_load_list_event_var_index = { LIST = test_list INDEX = index TARGET = my_event_target }`  
  
##### SLEX_store_list_event_var_index
`SLEX_store_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* TARGET : the scope to be saved to listname_index as an event_target (string). Default value : this
  
This saves the `target` and save it under the name `listname_index`. If you want to save an existing event_target, remember to specify `event_target:` in front of its name (see third example). Remember that it is not a global event_target.  
  
Example :  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index TARGET = this }`  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index } # This is the same as above`  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index TARGET = event_target:my_event_target }`  
  
### SLEX_dump_param
`SLEX_dump_param = {}`  
Accepted parameters :  
* EFFECT
* TYPE
* INDEX
* VALUE
* LIST
* FCT
* VARIABLE
  
This function allows you to "dump" the unused parameters from your scriped_effects. Just give the parameters that you didn't use. This is a left over from old code, but you can still use it. I will probably not remove it to ensure compatibility.  
  
Example :  
`SLEX_dump_param = { LIST = $LIST$ INDEX = $INDEX$ }`  
  
### Scripted_triggers
I added some scripted_triggers as well. This is under construction and only has some code for constant INDEX. If using your own scripted_triggers, beware of Stellaris inverting scripted_triggers result if they use a parameter. I highly recommend testing your scripted_triggers and these functions before uploading your mods.  
  
#### SLEX_list_is_empty
`SLEX_list_is_empty = { LIST = listname }`
* LIST : the list name (string)
  
This trigger is evaluated to True (after stellaris inversion) if the length of the list is 0. You could also simply do a check on listname_length. Just be careful about the inversion.  

#### SLEX_check_list_variable_const_index
`SLEX_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)  
  
This trigger is evaluated to True (after stellaris inversion) if `LIST_INDEX OPERATOR VALUE` is True.  
  
#### SLEX_inv_check_list_variable_const_index
`SLEX_inv_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)
  
This trigger is evaluated to False (after stellaris inversion) if `LIST_INDEX OPERATOR VALUE` is True.  
This trigger is here if you want to use "!=" in the non inverted trigger but can't, so you use this trigger with "=".  

#### SLEX_dump_param
`SLEX_dump_param = {  }`
Accepted parameters :
* EFFECT
* TYPE
* INDEX
* VALUE
* LIST
* FCT

This trigger allows you to "dump" the unused parameters from your scriped_triggers. Just give the parameters that you didn't use. It will always evaluate to True (after stellaris inversion).

## Legal Stuff
You are not allowed to put this mod without any modification on any website that has already one upload of this mod without my approval. This is only so there is one version on it for this repository. If you want to upload it as is to a new website (for example paradox mods), you are welcome to do so and please contact me so that I can put the link in this README. If you modified this mod, then you may also upload the modified version wherever you want as long as you specify that it isn't the original. Forking in github is of course allowed even if you didn't make any changes yet.  
  
You are not allowed to include this mod as is in your own mods (or modpacks) without my approval. This is here so that players don't have several versions of this mod. This could cause issues and code duplication. Set this mod as a dependancy instead, so that players only have one up-to-date version of this mod.  
  
You are allowed to use the technical ideas behind this mod for your own mods. If you need to adapt this mod to your needs, you are also allowed to include modified version of this mod in your mods. In this case make sure to not have any conflict with this mod. This means that players having both version activated should not get a single error line from them. But please take in mind that doing this means not having directly any update from this mod which means no performance update for your players. I would be very happy to include code that you think necessary in this mod (make some pull request or just contact me if you aren't familiar with github).  
  
You are allowed to use this mod and the ideas behind it for any type of content that is outside of Stellaris modding.  
  
You are not allowed to use this mod or modified version of this mod for any content that has a paywall or is begging for donations without my approval. Having a donation link is of course okay. Paradox Interactive has of course my approval.  

If you are using this mod, modified version of this mod or the technical concepts behind it, you have to put a link to this github repository or to the steam workshop mod. Having your mod list this mod as a dependancy via the steamworkshop is enough (no need for additionnal links in the description).  This is here so other modders can find this mod easily.  
  
As usual, I'm not responsable for any damage/incident resulting of the use of this mod, including (but not limited to) save corruption, achievements blocked and so on.
