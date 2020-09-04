This is the documentation for the (small) utilities part of SLEX. It might end up splitted in the future.  
  
# Adding resources with a variable
`SLEX_add_resource = { RESOURCE = resource_type AMOUNT = amount }`
* RESOURCE : the name of the resource (string)
* AMOUNT : the amount of the variable holding the resource (string)
  
You can also use an integer of float instead of a string for the AMOUNT, but you should just add the resource directly instead.  
This effect adds the AMOUNT of RESOURCE to the current scope with a precision of 0.01. ANything below that won't be added. This also works with negative amounts (i.e. to remove resources).  
  
Example :  
`SLEX_add_resource = { RESOURCE = energy AMOUNT = test_variable }`  
  
# Modulo and quotient
```
SLEX_division = {
    DIVIDEND = dividend_input
    DIVISOR = divisor_input
    QUOTIENT = quotient_output # optionnal
    REMAINDER = remainder # optionnal
    FRACTIONAL_REMAINDER = fractional_remainder # optionnal
}
```
* DIVIDEND : the dividend, either a variable or a value (string or integer or float)
* DIVISOR : the divisor, either a variable or a value (string or integer or float)
* QUOTIENT : the name of the variable where the quotient of the division should be stored (string) OPTIONNAL
* REMAINDER : the name of the variable where the remainder of the division should be stored (string) OPTIONNAL
* FRACTIONAL_REMAINDER : the name of the variable where the fractional remainder of the division should be stored (string) OPTIONNAL
  
In a division, you have `dividend/divisor = quotient + (remainder/divisor) = quotient + fractional_remainder` where `quotient` an integer and `0 <= fractional_remainder < 1`.  
This scripted effect allows you to get these 3 values in constant time (no loops).  
If `divisor` is 0, nothing will happen (no errors, variables won't change).  
  
Example :  
```
SLEX_division = {
    DIVIDEND = 10
    DIVISOR = 3
    QUOTIENT = quotient_output # will be 3
    REMAINDER = remainder # will be 1
    FRACTIONAL_REMAINDER = fractional_remainder # will be 1/3 = 0.33333
}
SLEX_division = {
    DIVIDEND = 4.2
    DIVISOR = 1.1
    QUOTIENT = quotient_output # will be 3
    REMAINDER = remainder # will be (4.2 - 3*1.1) = 4.2 - 3.3 = 0.9
    FRACTIONAL_REMAINDER = fractional_remainder # will be 0.9/1.1 = 0.81818
}
```