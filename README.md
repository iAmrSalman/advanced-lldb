# Advanced lldb
This is beginner guide to xcode lldb

## inspecting Objects

### po
print object description this is compiled expression that has a full access to the language 

#### Usage

``` bash
(lldb) po <object_name>
```
> you can also print a objcet child proprty value using dot notaion `po <object_name>.<property_name>`

#### customizing printed message
object should conferms to `CostomDebugStringConvertible` protocol which require one computed property (get only) called debugDescription and returns String.

``` swift
extension <type_name>: CostomDebugStringConvertible {
    var debugDescription: String {
        return "This is custom description for <type_name>"
    }
}
```

#### 

### p
print object using DataFormatters this is compiled expression that has a full access to the language 

#### Usage

``` bash
(lldb) p <object_name>
```
> you can also print a objcet child proprty value using dot notaion `p <object_name>.<property_name>`

### v
print object using DataFormatters this is not compiled expression and has no access to the language (FASTER!)

#### Usage

``` bash
(lldb) v <object_name>
```
> you can also print a objcet child proprty value using dot notaion `v <object_name>.<property_name>`


#### customizing DataFormatter
There is three options to customize DataFormatter

- Filters
- String summaries 
- synthetic children

##### Filters

###### adding filter

``` bash
(lldb) type filter add <target_name>.<type_name> --child <property_name>
(lldb) v <object_name>
```
> (<target_name>.<type_name>) <object_name> = (<property_name> = <property_value>)

###### removing filters

``` bash
(lldb) type filter delete <target_name>.<type_name> 
```

##### String summaries

###### adding summary

``` bash
(lldb) type summary add <target_name>.<type_name> --summary-string "has one property called <property_name> and its value is ${var.<property_name>}"
(lldb) v <object_name>
```
> (<target_name>.<type_name>) <object_name> = "has one property called <property_name> and its value is <property_value>"


## More info
- [WWDC18 Advanced debugging with Xcode and lldb](https://developer.apple.com/wwdc18/412)
- [WWDC19 lldb beyond po](https://developer.apple.com/wwdc19/429)

## Auther

[iAmrSalman](https://github.com/iAmrSalman), [iamrsalman@gmail.com](mailto:iamrsalman@gmail.com)
