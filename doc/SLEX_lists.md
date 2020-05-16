This is the documentation for the lists part of SLEX

# Sorting lists
  
## Sort of a list of variables
With SLEX, sorting a list of variables is pretty easy. Just use the `SLEX_sort_bubble` scripted_effect. Please remember to only use 2 levels of the stack when using it (reasons and explanations above). By default, the sort order is ascending. Use `ASCENDING = no` for descending order.  
  
Examples :  
`SLEX_sort_bubble = { LIST = listname }`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = yes } # same as above`  
`SLEX_sort_bubble = { LIST = listname ASCENDING = no }`  
  
## Sort of a list of event_targets zipped to a list of variables
What does that mean? Let's say you want to sort the empires by number of pops (like the example). You can count the number of pops easily (iterate with `every_owned_pop` for every country) and stock into a list. But once sorted, how can you say which country is first (except by recalculating the number of pops for every empire and checking every pair `(empire, value in the list)`).  
A good way to solve this problem is by saving the empires in a list of event_targets alongside the saving of their number of pops by using the same index. This means `pops_number_2` stores the number of pops of the empire stored in `targets_list_02`. We then say the list of events_target is *zipped* to the list of variables (even if there is no real link between the two outside of the modders code). Then you can't just sort the list of variables (`pops_number` in our example). Instead you sort the list of event_targets zipped with the list of variables. That means that after the zipped sort, `targets_list_2` will still have `pops_number_2` number of pops and that the list `pops_number` is sorted.  
  
There is however one limitation: there must not exist an index in the list where the zipped event_targets list doesn't have an event_target saved.  
  
Examples :  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname }`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = event_targets_listname ASCENDING = yes } # same as above`  
`SLEX_sort_zipped_target_list_bubble = { LIST = listname ZIPPED_LIST = list ASCENDING = no }`  
  
# Filling and reseting a list
Note : there is no concept of erasing event_targets, so there is no possibility in this mod to erase them.

## Reseting a list
`SLEX_reset_list_variable = { LIST = listname }`
* LIST : the list name (string)
  
Just fills the list with 0 and resets its length to 0.
  
Example :  
`SLEX_reset_list_variable = { LIST = test_list }`  
  
## Filling a list
`SLEX_fill_list_variable = { LIST = listname AMOUNT = amount VALUE = value}`
* LIST : the list name (string)
* AMOUNT : the value up to which (excluded) the list should be filled (either int/float, or a variable ie a string)
* VALUE : the value used to fill the list (either int/float, or a variable ie a string)
  
Just fills the list with VALUE from 0 to AMOUNT-1 : it sets its length to AMOUNT.  
  
Example :  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = 5 VALUE = 42 }`  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = 3 VALUE = my_variable }`  
`SLEX_fill_list_variable = { LIST = test_list AMOUNT = some_variable VALUE = my_variable }`  
  
## Filling a zipped list
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
  
# Handling a list like a FILO (first in last out)
If you want to handle the list like a pile, you can!  
  
## Append to a list
`SLEX_append_list_variable = { LIST = listname VALUE = value }`
* LIST : the list name (string)
* VALUE : the value appended to the list (either int/float, or a variable ie a string)
  
Appends VALUE to the end of the list and increments its length by one.  
  
Example :  
`SLEX_append_list_variable = { LIST = test_list VALUE = 42 }`  
`SLEX_append_list_variable = { LIST = test_list VALUE = my_var }`  
  
## Append to a zipped list
`SLEX_append_list_zipped = { LIST = listname ZIPPED_LIST = zipped_list_name VALUE = value TARGET = target }`
* LIST : the list name (string)
* ZIPPED_LIST : the name of the list of event_targets (string)
* VALUE : the value appended to the list (either int/float, or a variable ie a string)
* TARGET : the event_target appended to the zipped list (string). Defaults to `this`
  
Appends VALUE to the end of the list and TARGET (defaults to `this`) at the end or the zipped list and increments its length by one.  
  
Example :  
`SLEX_append_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev VALUE = 42 TARGET = event_target:ev_target }`  
`SLEX_append_list_zipped = { LIST = test_list ZIPPED_LIST = test_ev VALUE = my_variable }`  
  
## Pop from a list
`SLEX_pop_list_variable = { LIST = listname VALUE = value }`
* LIST : the list name (string)
* VALUE : the variable where the popped value should be stored (string)
  
Pops the last element of the list (stores it to the VALUE variable and replace it by 0) and decrements its length by one. If the list was empty, does nothing instead.  
  
Example :  
`SLEX_pop_list_variable = { LIST = test_list VALUE = output_variable }`  
  
## Pop from a zipped list
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
  
# Accessing the stored variables/event_targets
You may ask "But I can't just write `listname_variable`! How can I access a certain index using a variable ?"  
And that's the core of the mod. Due to the same stellaris limitation, you would have to use a big switch (here it is an if/else tree). That's why the code uses an integar division (despite modulo not being available directly in stellaris) to convert a variable index into a string that can be used to get the variable. See `common/scriped_effects/SLEX_scripted_effects.txt` and `common/scriped_effects/SLEX_scripted_effects_auxiliary.txt` for more details.  
  
## Using constant index
As seen, you can directly use the list name followed by an underscore followed by the index to access the variable. However, there are some scripted_effects that this mod adds. Please remember that you must currently use `listname_0` and not`listname_00`. This means that when using a constant index, make sure to always have the least amount of digit number (minimum of 1). The digits are in hexadecimal, so 0, 1, ..., 9, a, b, c, d, e, f.  
  
### SLEX_get_list_variable_const_index
`SLEX_get_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the variable where to write the listname_index (string)
  
Example :  
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_output }`  
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_output }`  
  
### SLEX_set_list_variable_const_index
`SLEX_set_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
### SLEX_change_list_variable_const_index
`SLEX_change_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
### SLEX_subtract_list_variable_const_index
`SLEX_subtract_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = test_input }`  
  
### SLEX_multiply_list_variable_const_index
`SLEX_multiply_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 51 VALUE = 3 }`  
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 1 VALUE = test_input }`  
  
### SLEX_divide_list_variable_const_index
`SLEX_divide_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value by which listname_index should be divided (either int/float, or a variable ie a string)
  
Example :  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`  
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 17 VALUE = test_input }`  
  
### SLEX_load_list_event_const_index
`SLEX_load_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the event_target name to be used when saving listname_index as an event_target (string)
  
This loads the `listname_index` and save it under the name `target` as an event_target. Remember that it is not a global event_target.  
  
Example :  
`SLEX_load_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = my_event_target }`  
  
### SLEX_store_list_event_const_index
`SLEX_store_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the index (integer)
* TARGET : the scope to be saved to listname_index as an event_target (string). Default value : this
  
This saves the `target` and save it under the name `listname_index`. If you want to save an existing event_target, remember to specify `event_target:` in front of its name (see third example). Remember that it is not a global event_target.  
  
Example :  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = this }`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 } # This is the same as above`  
`SLEX_store_list_event_const_index = { LIST = test_list INDEX = 7 TARGET = event_target:my_event_target }`  
  
## Using variable index
So this is really the core of the mod. The first functions are basically the same as for constant index, except now the INDEX has to be a variable (ie a string corresponding to the name of the variable). The value of index has to be between 0 and 255 included.  
  
### SLEX_get_list_variable_var_index
`SLEX_get_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the variable where to write the listname_index (string)
  
Example :  
`SLEX_get_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_output }`  
  
### SLEX_set_list_variable_var_index
`SLEX_set_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_set_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_set_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_input }`  
  
### SLEX_change_list_variable_var_index
`SLEX_change_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_change_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_change_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_add }`  
  
### SLEX_subtract_list_variable_var_index
`SLEX_subtract_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_subtract_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_subtract_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_subtract }`  
  
### SLEX_multiply_list_variable_var_index
`SLEX_multiply_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)
  
Example :  
`SLEX_multiply_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_multiply_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_multiply }`  
  
### SLEX_divide_list_variable_var_index
`SLEX_divide_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the value by which listname_index should be divided (either int/float, or a variable ie a string)
  
Example :  
`SLEX_divide_list_variable_var_index = { LIST = test_list INDEX = index VALUE = 42 }`  
`SLEX_divide_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_divide }`  
  
### SLEX_load_list_event_var_index
`SLEX_load_list_event_var_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* TARGET : the event_target name to be used when saving listname_index as an event_target (string)
  
This loads the `listname_index` and save it under the name `target` as an event_target. Remember that it is not a global event_target.  
  
Example :  
`SLEX_load_list_event_var_index = { LIST = test_list INDEX = index TARGET = my_event_target }`  
  
### SLEX_store_list_event_var_index
`SLEX_store_list_event_const_index = { LIST = listname INDEX = index TARGET = target }`  
* LIST : the list name (string)
* INDEX : the variable index (string)
* TARGET : the scope to be saved to listname_index as an event_target (string). Default value : this
  
This saves the `target` and save it under the name `listname_index`. If you want to save an existing event_target, remember to specify `event_target:` in front of its name (see third example). Remember that it is not a global event_target.  
  
Example :  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index TARGET = this }`  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index } # This is the same as above`  
`SLEX_store_list_event_var_index = { LIST = test_list INDEX = index TARGET = event_target:my_event_target }`  
  
# Scripted triggers
I added some scripted_triggers as well. This is under construction and only has some code for constant INDEX. If using your own scripted_triggers, beware of Stellaris inverting scripted_triggers result if they use a parameter. I highly recommend testing your scripted_triggers and these functions before uploading your mods. These triggers take the inversion into account so that you don't have to worry about it (unless it was fixed in a Stellaris update).  
  
## Checking if a list is empty
`SLEX_list_is_empty = { LIST = listname }`
* LIST : the list name (string)
  
This trigger is evaluated to True (after stellaris inversion) if the length of the list is 0. You could also simply do a check on listname_length.
  
## SLEX_check_list_variable_const_index
`SLEX_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)  
  
This trigger is evaluated to True (after stellaris inversion) if `LIST_INDEX OPERATOR VALUE` is True.  
  
## SLEX_inv_check_list_variable_const_index
`SLEX_inv_check_list_variable_const_index = { LIST = listname INDEX = index OPERATOR = operator VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* LIST : the operator (=, <, >, <=, >= There is no != !!!)
* VALUE : the value to compare to listname_index (either int/float, or a variable ie a string)
  
This trigger is evaluated to False (after stellaris inversion) if `LIST_INDEX OPERATOR VALUE` is True.  
This trigger is here if you want to use "!=" in the non inverted trigger but can't, so you use this trigger with "=".  
