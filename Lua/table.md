## Lua table
Lua table的[官方定义](http://www.lua.org/manual/5.2/manual.html#3.4.8)如下：
```
tableconstructor ::= ‘{’ [fieldlist] ‘}’
```
```
fieldlist ::= field {fieldsep field} [fieldsep]
```
```
field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp
```
```
fieldsep ::= ‘,’ | ‘;’
```

通过上述定义可知：
* 每个**field**之间通过','隔开
* 索引值需要使用"\["和"\]"括起来，如果是字符串，可以去掉字符串的引号和中括号
* 索引除了**nil**可以是任意值
* 如果没有索引，则认为是索引的值，自动从1依次往后编号

### 示例
示例1：
```lua
table = {}  -- simplest table
```

示例2：
```
table = {
	name = "Frank",
	["location"] = "London"
}
```

示例3：
```
table = {
	name = "Frank",
	["location"] = "London", -- error if: "location" = "London"
	20,		-- index 1
	scores = { 60, 80, 70},
	50,		-- index 2
	[2] = 111
}
```
