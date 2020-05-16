# Scripted effects
  
## SLEX_dump_param
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
  
# Scripted triggers
  
## SLEX_dump_param
`SLEX_dump_param = {  }`
Accepted parameters :
* EFFECT
* TYPE
* INDEX
* VALUE
* LIST
* FCT

This trigger allows you to "dump" the unused parameters from your scriped_triggers. Just give the parameters that you didn't use. It will always evaluate to True (after Stellaris inversion).  
  