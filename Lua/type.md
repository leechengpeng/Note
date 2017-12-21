## 基本类型

### 字符串
字符串的几种定义如下：
1. 单引号，其中'\n'表示转义字符
```lua
str = 'abc\ndef'
```

2. 双引号
```lua
str = "\97\98c"
```

3. "[[XXX]]"来表示字符串"XXX"，字符串"XXX"可包含任意字符
```lua
str = [[it's mine!
it's not yours! --'Oh'   --"Wow!!!!"
]]
```

4. "[==[XXX]==]"表示字符串，定义3有个问题就是不能包含"[["或"]]"，因此提供添加"="的方式来表示字符串。这种形式的"="可以是任意数量，但前后"="数量必须一致
```lua
str1 = [==[abc[=]defgh[[=]]]==]   --2
str2 = [=[abc[=]defgh[[=]]]=]     --1
str3 = [===[abc[=]defgh[[=]]]===] --3
```

## 注释
1. "--"表示注释
```lua
-- Single line comment
```

2. "--[[XXX]]"表示多行注释
```lua
--[[Multi line 
comment]]
```

3. 类似string一样，2中的多行注释也不能包含"[["或"]]"。因此，也通过添加"="来避免该问题
```lua
--[=[Multi line 
comment,
where contains "]]" or "[["
]=]
```
