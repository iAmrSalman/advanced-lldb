# Advanced (lldb)

This guide contains the following content to ease your beginner journey to (lldb):

- [x] Object inspection 
- [x] Code modification
- [x] Breakpoints manipulation
- [x] Tips and tricks

## Object inspection

In this section we will use po, p and v commands to inspect any suspicious objects in your debugging journey. 

### (lldb) po <expression>

print object description this is compiled expression that has a full access to the language 

#### Usage

``` bash
(lldb) po <object_name>
```
> you can also print a objcet child proprty value using dot notaion `po <object_name>.<property_name>`

> also make modification to your code 'po <object_name>.boolProperty = false'

#### customizing printed message

object should conferms to `CostomDebugStringConvertible` protocol which require one computed property (get only) called debugDescription and returns String.

``` swift
extension <type_name>: CostomDebugStringConvertible {
    var debugDescription: String {
        return "This is custom description for <type_name>"
    }
}
```

### (lldb) p <expression>

this is compiled expression that has a full access to the language and output LLDB-formatted description (DataFormatter)

#### Usage

``` bash
(lldb) p <object_name>
```
> you can also print a objcet child proprty value using dot notaion `p <object_name>.<property_name>`

### (lldb) v <name>

reads value of <name> from memory and output LLDB-formatted description (FASTER!)

#### Usage

``` bash
(lldb) v <object_name>
```
> you can also print a objcet child proprty value using dot notaion `v <object_name>.<property_name>`


#### customizing DataFormatter

There is three options to customize DataFormatter (used by commands p and v)

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

## Code modification

In this section we will use expression command to make changes to your code only inside the debugging session. 

### (lldb) expression

This allows you to execute arbitrary code in the debugger.

#### Usage

``` bash
(lldb) expression isThisreal = false
```
## Breakpoints manipulation

In this section you'll learn how to create, modify, and delete breakpoints. 

### Create

#### (lldb) breakpoint set 

This command deletes all breakpoints.

##### Usage

``` bash
(lldb) breakpoint set -n "-[NSView hitTest:]" -C "po $rdi" -G1
```

this command says to create a breakpoint on -[NSView hitTest:], have it
execute the "po $rdi" command which instructs LLDB to print out the contents of the object at the memory
address referenced by what’s stored in the RDI assembly register., then automatically continue after executing the
command.

The following registers are used as parameters when a function is called in x64 assembly. Try and commit these to memory, as you’ll use these frequently in the future:

- First Argument: RDI
- Second Argument: RSI
- Third Argument: RDX
- Fourth Argument: RCX
- Fifth Argument: R8
- Sixth Argument: R9

`-G0`: says to the breakpoint to not automatically resume execution after the action has been performed

`-G1`: says to the breakpoint to automatically resume execution after the action has been performed

``` bash
(lldb) breakpoint set --one-shot true --name "-[NSView hitTest:]"
```
this command says to create a one shot breakpoint on -[NSView hitTest:] will be deleted automatically.

### Modify

#### (lldb) breakpoint modify

This command modify existing breakpoints.

##### Usage

``` bash
(lldb) breakpoint modify -c '(BOOL)[NSStringFromClass((id)[$rdi class])
containsString:@"IDESourceEditorView"]' -G0
```
This command modifies all existing breakpoints in your debugging session and creates a condition which gets evaluated everytime -[NSView hitTest:] fires. If the condition evaluates to true, then execution will pause in the debugger. This condition checks that the instance of the NSView is of type IDESourceEditorView. The final -G0 says to modify the breakpoint to not automatically resume execution after the action has been performed.

### Delete

#### (lldb) breakpoint delete

This command deletes all breakpoints.

##### Usage

``` bash
(lldb) breakpoint delete
```

## Tips and tricks

This section contains some tips and tricks i'd like to use to enhance my debugging workflow . 

### (lldb) continue

This command will resume execution.

#### Usage

``` bash
(lldb) continue
```

or `c` for short

``` bash
(lldb) c
```

### assembly registers

I do forget this assembly registers all the time but fortunately there is a way to not use them.

``` bash 
(lldb) po $arg1
```

- arg1 -> rdi
- arg2 -> rsi
- arg3 -> rdx
- arg4 -> rcx
- arg5 -> r8
- arg6 -> r9

There is a special case for arg2 which represents the selector, because of lldb doesn't know the type of this arguments you need to explictly type cast it.

``` bash
(lldb) po (SEL)$arg2
```

### Replacing code

I can't count how many times i needed to replace a line of code and put another.

``` bash 
(lldb) thread jump --by 1
```

This will skip one line.

``` bash
(lldb) expression isThisreal = false
```

### New tab for Debug

This is very useful feature in Xcode, you can make xcode create new tab for debug automatically

goto xcode menu >> prefrances >> behaviors >> Pauses >> check Show tab named [Debug] in [Active window]

### UI manipulation

1. Get the memory address for this view.

``` bash
(lldb) expression -l objc -o -- [`self.view` recursiveDescription]
```

2. use view's memory address to modify its properties.

``` bash
(lldb) expression -l objc -o -- 0x7fb3afc40e90
```

there is another way to do this 

``` bash
(lldb) po unsafeBitCast(, to: <type_name>.self)
```

now you can modify any property

``` bash
(lldb) po unsafeBitCast(, to: <type_name>.self).center.y = 300
```

3. Finally update the screen's frame buffer

``` bash 
(lldb) expression CATransaction.flush()
```

### Aliases

This is very useful to shortening long commands, po in objective c is very good example .

``` bash
(lldb) command alias poc expression -l objc -o --
```

now i can do this

``` bash
(lldb) poc 0x7fb3afc40e90
```

## More info

- [WWDC18 Advanced debugging with Xcode and lldb](https://developer.apple.com/wwdc18/412)
- [WWDC19 lldb beyond po](https://developer.apple.com/wwdc19/429)
- [raywenderlich's Apple Debugging and Reverse Engineering](https://store.raywenderlich.com/products/advanced-apple-debugging-and-reverse-engineering)

## Auther

[iAmrSalman](https://github.com/iAmrSalman), [iamrsalman@gmail.com](mailto:iamrsalman@gmail.com)
