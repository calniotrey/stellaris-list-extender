This is the documentation for the (small) utilities part of SLEX. It might end up splitted in the future.  
  
# Adding resources with a variable
`SLEX_add_resource = { RESOURCE = resource_type AMOUNT = amount }`
* RESOURCE : the name of the resource (string)
* AMOUNT : the amount of the variable holding the resource (string)
  
You can also use an integer of float instead of a string for the AMOUNT, but you should just add the resource directly instead.  
This effect adds the AMOUNT of RESOURCE to the current scope with a precision of 0.01. ANything below that won't be added. This also works with negative amounts (i.e. to remove resources).  
  
Example :  
`SLEX_add_resource = { RESOURCE = energy AMOUNT = test_variable }`  
  