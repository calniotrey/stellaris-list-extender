# Get custom parameter
As stated in the README, there is 2 different types of scripted_effects depending on the nature of the trigger condition. For each types, there is 4 different scripted_effects to choose the best resolution and range. Select the one you need by appending a suffix at the end of its name:  
* To get an integer resolution of the type : `x * (16^exponent)`, use the default one (no suffix to append) and specify the exponent by using the ROUNDING_ZEROES parameter and giving it as many zeroes as the value of the exponent ("00" for a resolution of 16^2 = 256). Don't use the ROUNDING_ZEROES if you want a resolution of 1. Please note that the maximum x value is +/-255 (except when using the "_4096" suffixe where it i +/-4095).  
* To get a resolution of 0.1, use the suffixe "_decimal". The value stored to the variable is between -15.9 and 15.9 and has a resolution of 0.1.  
* To get a resolution of 0.01, use the suffixe "_percentile". The value stored to the variable is between -1 and 1 and has a resolution of 0.01.  
* To get a range for x of +/-4095 with a resolution of 1 , use the suffixe "_4096". The value stored to the variable is between 4095 and 4095 and has a resolution of 1. Please do not write this often in your code as it generates a big compiled scripted effect. You can however execute it as much as you want.  
  
## Simple trigger condition
```
SLEX_get_custom_parameter = {
    CONDITION_NAME = trigger_condition
    VARIABLE = variable_name
    ROUNDING_ZEROES = ROUNDING_ZEROES
}
```
* trigger_condition : the name of the trigger condition (string)
* variable_name : the name of the variable where to write the result (string)
* rounding_zeroes : must be only "0", "00", ... For each "0" in it, it will mutliply the resolution by 16. If you don't want any rounding_zeroes, just omit it. (string) defaults to ""
  
Use this for something like income that could be checked directly with `CONDITION_NAME < value`, use it like this :  
`SLEX_get_custom_parameter = { CONDITION_NAME = income VARIABLE = output_income }`  
This will return the income in the range `[-255:255]` (note : income can't actually be negative).  
`SLEX_get_custom_parameter = { CONDITION_NAME = income VARIABLE = output_income ROUNDING_ZEROES = 0 }`  
This will return the income in the range `[-4080:4080]` as a multiple of 16 (note : income can't actually be negative).  
You can also use the "_decimal" or "_percentile" suffixe:
`SLEX_get_custom_parameter_decimal = { CONDITION_NAME = used_naval_capacity_percent VARIABLE = output }`  
`SLEX_get_custom_parameter_percentile = { CONDITION_NAME = used_naval_capacity_percent VARIABLE = output }`  
  
## Advanced trigger condition
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = trigger_condition
    FIRST_KW = first_keyword
    SECOND_KW = second_keyword
    THIRD_KW = third_keyword
    VARIABLE = variable_name
    ROUNDING_ZEROES = ROUNDING_ZEROES
}
```  
* trigger_condition : the name of the trigger condition (string)
* first_keyword : the name of the first keyword of the trigger condition (string)
* second_keyword : the name of the second keyword of the trigger condition (string) OPTIONNAL
* third_keyword : the name of the third keyword of the trigger condition (string) OPTIONNAL
* variable_name : the name of the variable where to write the result (string)
* rounding_zeroes : must be only "0", "00", ... For each "0" in it, it will mutliply the resolution by 16. If you don't want any rounding_zeroes, just omit it. (string) defaults to ""
  
Use this for something like minerals reserve that could be checked directly with :  
```
CONDITION_NAME = {
    FIRST_KW = SECOND_KW
    THIRD_KW < value
}
```  
  
Use it like this (if you want the amount to be at max 255, remove ROUNDING_ZEROES. If you want it to be at max 4080, keep it, if you want it to be at max 65 280, put 00 instead and so on...) :  
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = has_resource
    FIRST_KW = type
    SECOND_KW = minerals
    THIRD_KW = amount
    VARIABLE = variable_name
    ROUNDING_ZEROES = 0
}
```  
  
You can also use the "_decimal" or "_percentile" suffixe:
```
SLEX_get_custom_parameter_advanced_decimal = {
    CONDITION_NAME = has_monthly_income
    FIRST_KW = resource
    SECOND_KW = influence
    THIRD_KW = value
    VARIABLE = SLEX_example_influence_income
}
```  
```
SLEX_get_custom_parameter_advanced_percentile = {
    CONDITION_NAME = has_monthly_income
    FIRST_KW = resource
    SECOND_KW = influence
    THIRD_KW = value
    VARIABLE = SLEX_example_influence_income
}
```  
  
You can also use this for something like species count (without limit) that could be checked directly with :  
```
CONDITION_NAME = {
    FIRST_KW < value
}
```  
  
In that case **DO NOT** use SECOND_KW and THIRD_KW, but use it like this (here ROUNDING_ZEROES is "" as you probably just want the exact count) :  
```
SLEX_get_custom_parameter_advanced = {
    CONDITION_NAME = count_species
    FIRST_KW = count
    VARIABLE = variable_name
}
```  
  