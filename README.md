# Stellaris List EXtender  
This mod is a utility mod. It adds functions to allow modders to use lists.

## Requirements
None

## Compatibility
No compatility problem
Not iron-man compatible

## How to use/test mod
1. Open the launcher from stellaris.
2. Go to mods > Mod tools
3. Click `Create Mod`
4. Fill the blanks and click create
5. Go to the Stellaris mods folder (in my case `...\Documents\Paradox Interactive\Stellaris\mod\`).
6. Extract the content of the repository inside of the newly created mod folder
7. Activate the mod

## Detailed description
This mod allows modders to use lists. A list is characterized by it's name (for example `resources`). A list has the same scope rules as a variable.  
To access a certain index of the list, you can use `listname_index`. A list is basically just a collection of variables beginning with the list name followed by an underscore.  
Due to stellaris limitations, index has to be between 0 (included) and 127 (included). I will release another mod with higher limit (it makes the mod bigger).  
  
You may ask "But how can I access a certain index using a variable ?"  
And that's the core of the mod. Due to the same stellaris limitation, there is a big switch on the index (that's the reason why the index has to be in [0:127]). That switch is generated as a if/else binary tree in order to maximize speed.  

### Using constant index
As seen, you can directly use the list name followed by an underscore followed by the index to access the variable.  However, there are some scripted_effects that this mod adds :

#### SLEX_get_list_variable_const_index
`SLEX_get_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the variable where to write the listname_index (string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_get_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_output }`

#### SLEX_set_list_variable_const_index
`SLEX_set_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to write to listname_index (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`
`SLEX_set_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_input }`

#### SLEX_change_list_variable_const_index
`SLEX_change_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to add to listname_index (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`
`SLEX_change_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_input }`

#### SLEX_subtract_list_variable_const_index
`SLEX_subtract_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to subtract to listname_index (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`
`SLEX_subtract_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_input }`

#### SLEX_multiply_list_variable_const_index
`SLEX_multiply_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to multiply to listname_index (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`
`SLEX_multiply_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_input }`

#### SLEX_divide_list_variable_const_index
`SLEX_divide_list_variable_const_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : the value to divide to listname_index (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = 3 }`
`SLEX_divide_list_variable_const_index = { LIST = test_list INDEX = 7 VALUE = test_input }`

#### SLEX_apply_effect_list_variable_const_index
`SLEX_apply_effect_list_variable_const_index = { EFFECT = effect LIST = listname INDEX = index VALUE = value }`
* EFFECT : the effect to apply (scriped_effect ie a string)
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : another parameter to use in the EFFECT (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...).  
This will execute the scriped_effect EFFECT while giving him the parameters LIST, INDEX, VALUE. This is of little use for modders, but is used by the mod.  
If you use this function, you should use ALL parameters given in the scripted_effect (or else stellaris might throw an error). If you don't know what to do with some of them, use the `SLEX_dump_param = { LIST = $LIST$ INDEX = $INDEX$ VALUE = $VALUE$}` inside of your scripted_effect.

Example :
`SLEX_apply_effect_list_variable_const_index = { EFFECT = test_effect LIST = test_list INDEX = 7 VALUE = 10 }`
`SLEX_apply_effect_list_variable_const_index = { EFFECT = test_effect LIST = test_list INDEX = 7 VALUE = test_param }`
Example for a scripted_effect :
```test_effect = {
    if = {
        limit = {
            check_variable = {
                which = $LIST$_$INDEX$
                value > $VALUE$
            }
        }
        owner = { add_resource = { energy = 100 } }
    }
}
```

#### SLEX_apply_effect_conditionnal_list_variable_const_index
`SLEX_apply_effect_conditionnal_list_variable_const_index = { EFFECT = effect TRIGGER = trigger LIST = listname INDEX = index VALUE = value }`
* EFFECT : the effect to apply (scriped_effect ie a string)
* TRIGGER : the trigger to test (scriped_trigger ie a string)
* LIST : the list name (string)
* INDEX : the index (integer)
* VALUE : another parameter to use in the EFFECT (either int/float, or a variable ie a string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...).  
If the Trigger is evaluated to True, then this will execute the scriped_effect EFFECT while giving him the parameters LIST, INDEX, VALUE. This is of little use for modders, but is used by the mod.  
If you use this function, you should use ALL parameters given in the scripted_effect and in the scripted_trigger (or else stellaris might throw an error). If you don't know what to do with some of them, use the `SLEX_dump_param = { LIST = $LIST$ INDEX = $INDEX$ VALUE = $VALUE$}` inside of your scripted_effect.  
Be careful to have your scripted_trigger inversed as scripted_triggers using variables are inverted by Stellaris for some reason.  

Example :
`SLEX_apply_effect_conditionnal_list_variable_const_index = { EFFECT = test_effect TRIGGER = test_trigger LIST = test_list INDEX = 7 VALUE = 10 }`
`SLEX_apply_effect_conditionnal_list_variable_const_index = { EFFECT = test_effect TRIGGER = test_trigger LIST = test_list INDEX = 7 VALUE = test_param }`
Example for a scripted_effect :
```test_effect = {
    if = {
        limit = {
            check_variable = {
                which = $LIST$_$INDEX$
                value > $VALUE$
            }
        }
        owner = { add_resource = { energy = 100 } }
    }
}
```

## Using variable index
So this is really the core of the mod. The first functions are basically the same as for constant index, except now the INDEX has to be a variable (ie a string).  

### SLEX_get_list_variable_var_index
`SLEX_get_list_variable_var_index = { LIST = listname INDEX = index VALUE = value }`
* LIST : the list name (string)
* INDEX : the variable index (string)
* VALUE : the variable where to write the listname_index (string)

For some reason, Stellaris doesn't like 1 letter variable (so no i/a/b/c/...)

Example :
`SLEX_get_list_variable_var_index = { LIST = test_list INDEX = index VALUE = test_output }`

### SLEX_set_list_variable_var_index
### SLEX_change_list_variable_var_index
### SLEX_subtract_list_variable_var_index
### SLEX_multiply_list_variable_var_index
### SLEX_divide_list_variable_var_index
### SLEX_apply_effect_list_variable_var_index
### SLEX_apply_effect_conditionnal_list_variable_var_index UNTESTED

## SLEX_dump_param
`SLEX_dump_param = {}`
Accepted parameters :
* EFFECT
* TYPE
* INDEX
* VALUE
* LIST
* FCT

This function allows you to "dump" the unused parameters from your scriped_effects. Just give the parameters that you didn't use.

Example :
`SLEX_dump_param = { LIST = $LIST$ INDEX = $INDEX$ }`

## Scripted_triggers
Under construction. Don't use yet.

## Legal Stuff
You are not allowed to put this mod on any site without my approval. This is only so there is one version of it for this repository.  
You are allowed to make modifications of this mod and put it anywhere you want. You are only required to put a link to this github repository (so users can find other forks if they want too).  
You are allowed to use this mod for any type of content (so outside of Stellaris modding).  
However, you are not allowed to use this mod for any content that generates money without my approval. This has priority over the other rules. Paradox Interactive has of course my approval.  
As usual, I'm not responsable for any damage/incident resulting of the use of this mod, including (but not limited to) save corruption, achievements blocked and so on.
