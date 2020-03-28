# Stellaris List EXtender  
This mod is a utility mod. It adds functions to allow modders to use lists of variables or event_targets and to sort them. See "Examples" for some examples of what you can do.  
  
## Requirements
None  
  
## Compatibility
No compatility problem
Not iron-man compatible
  
## How to use/test mod
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
1 | Index is not between 0 and 99 included
101 | The event_target doesn't exist (check the scope and the event chain) using constant index
102 | The event_target doesn't exist (check the scope and the event chain) using variable index
  
## Detailed description : Read before using
This mod allows modders to use lists of variables and lists of (non-global) event_targets.  
A list (both for variables and for event_targets) is characterized by its name (for example `resources_list`). A list of variables has the same scope rules as a variable. A list of event_targets has the same rules as a non-global event_target: it only lasts in the unbroken event chain.  
To access a certain index of the list, you can use `listname_index`. A list is basically just a collection of variables beginning with the list name followed by an underscore. However, the index **must** have 2 digits (so 00, 01, 02, ... 09, 10, ...).  
A quick note about a little known Stellaris modding limitation: scripted_effects (and probably triggers, too) can only have a **maximum of 5 levels of the stack**. This means that if a scripted_effect named A calls B, B calls C, C calls D, D calls E, then any call from E to another scripted_effect F will make the game consider F as an unknown scripted_effect and will (probably) break the whole scripted_effect chain.  
Due to this limitation, the index has to be between 00 (included) and 99 (included). I'm considering to switch to a hexadecimal base (currently decimal) in the near future to allow a higher number of elements (256). However, I want to optimize the sort algorithm first.  
The highest index (99) is defined by the number of stack levels SLEX uses to get and set the variables/event_targets. The getter and setter (and all similar functions such as change/multiply/...) use 1 stack level for constant index and 2 stack levels for variable index. The sort function uses 3 stack levels. This means that when writing your scripted_effects, you should have a maximum of 2 stack levels when using the sort functions, 3 when using getter/setter with a variable index and 4 when using a constant index.  
One last warning before closing the "Must read" part: For some reason, Stellaris doesn't like 1 letter variable. So please do not use i/a/b/c/...  
  
## Examples
List of the ingame examples:  
One non-localised edict: It ranks the empires by descending number of pops and give 1 pop per distance in the ranking to the first. That means the empire with the most amount of pops gets 0 pops, the second gets 1 pop, and so on. Only default empires as well as (awakaned) fallen empires are taken into account. These empires also need to have a capital_scope (which they probably have). When active, you have a 10 days delay to get into observer mode before the event fires.  
  
To access the ingame examples, open the console and type `effect set_global_flag = SLEX_example`.  
  
## Documentation
Now let's look into the detailed documentation by beginning with the promised sorting functions :  
  
### Sort
  
#### Sort of a list of variables
With SLEX, sorting a list of variables is pretty easy. Just use the `SLEX_sort_bubble` scripted_effect. Please remember to only use 2 levels of the stack when using it (reasons and explanations above). By default, the sort order is ascending. Use `ASCENDING = no` for descending order.  
  
Examples :  
`SLEX_sort_bubble = { LIST = listname }`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = yes } # same as above`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = no }`  
  
#### Sort of a list of event_targets zipped to a list of variables
What does that mean? Let's say you want to sort the empires by number of pops (like the example). You can count the number of pops easily (iterate with `every_owned_pop` for every country) and stock into a list. But once sorted, how can you say which country is first (except by recalculating the number of pops for every empire and checking every pair `(empire, value in the list)`).  
A good way to solve this problem is by saving the empires in a list of event_targets alongside the saving of their number of pops by using the same index. This means `pops_number_02` stores the number of pops of the empire stored in `targets_list_02`. We then say the list of events_target is *zipped* to the list of variables (even if there is no real link between the two outside of the modders code). Then you can't just sort the list of variables (`pops_number` in our example). Instead you sort the list of event_targets zipped with the list of variables. That means that after the zipped sort, `targets_list_02` will still have `pops_number_02` number of pops and that the list `pops_number` is sorted.  
  
Examples :  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname }`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname ASCENDING = yes } # same as above`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = list ASCENDING = no }`  
  
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
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 07 VALUE = test_output }`  
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_output }`  
  
##### SLEX_set_list_variable_const_index
`SLEX_set_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 07 VALUE = 3 }`  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_change_list_variable_const_index
`SLEX_change_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 07 VALUE = 3 }`  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_subtract_list_variable_const_index
`SLEX_subtract_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 07 VALUE = 3 }`  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
##### SLEX_multiply_list_variable_const_index
`SLEX_multiply_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = 3 }`  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 01 VALUE = test_input }`  
  
##### SLEX_divide_list_variable_const_index
`SLEX_divide_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value by which listname_index should be divided (either int/float, or a variable ie a string)
  
Example :  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 07 VALUE = 3 }`  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 17 VALUE = test_input }`  
  
##### SLEX_load_list_event_const_index
`SLEX_load_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the event_target name to be used when saving listname_index as an event_target (string)
  
This loads the `listname_index` and save it under the name `target` as an event_target. Remember that it is not a global event_target.  
  
Example :  
`SLEX_load_list_event_const_index = { LIST = test_list INDEX = 07 TARGET = my_event_target }`  
  
##### SLEX_store_list_event_const_index
`SLEX_store_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the scope to be saved to listname_index as an event_target (string). Default value : this
  
This saves the `target` and save it under the name `listname_index`. If you want to save an existing event_target, remember to specify `event_target:` in front of its name (see third example). Remember that it is not a global event_target.  
  
Example :  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 07 TARGET = this }`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 07 } # This is the same as above`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 07 TARGET = event_target:my_event_target }`  
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
  
#### SLEX_check_list_variable_const_index
`SLEX_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)  
  
This trigger is evaluated to True (after stellaris inversion) is `LIST_INDEX OPERATOR VALUE` is True.  
  
#### SLEX_inv_check_list_variable_const_index
`SLEX_inv_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)
  
This trigger is evaluated to False (after stellaris inversion) is `LIST_INDEX OPERATOR VALUE` is True.  
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
You are not allowed to put this mod on any site without my approval. This is only so there is one version of it for this repository.  
You are allowed to make modifications of this mod and put it anywhere you want. You are only required to put a link to this github repository (so users can find other forks if they want too).  
You are allowed to use this mod for any type of content (so outside of Stellaris modding).  
However, you are not allowed to use this mod for any content that generates money without my approval. This has priority over the other rules. Paradox Interactive has of course my approval.  
As usual, I'm not responsable for any damage/incident resulting of the use of this mod, including (but not limited to) save corruption, achievements blocked and so on.
