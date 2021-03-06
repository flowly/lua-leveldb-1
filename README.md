lua-leveldb
===========

Lua bindings for google's leveldb key/store database.

From the googlecode page:
"The leveldb library provides a persistent key value store. Keys and values are arbitrary byte arrays.
The keys are ordered within the key value store according to a user-specified comparator function."

I wrote an extension because i needed a fast embeddable key/value database to use it with WireShark for packet analysis.
I needed to analyze some big pcap files and divide the traffic using some given rules.
So i wrote this basic lua extension to access Leveldb functions directly from lua that's used as extension language for WireShark.

Most of the basic options and functions are supported right now, but still not the full set of operations permitted by Leveldb.

Support
-----
The extension still not support the full set of operations permitted by the C++ library.
This is what is on business:  
  * Basic open/close/repair database operations supported.  
  * Basic database write, read and delete operations.
  * Partial access to most important options settings is supported.
  * Iterators with seek and iteration operations are supported.
  * Atomic batch updates supported.
  * Experimental support for unique values.

Basic Example
-----
This is a simple example on how to use the lua extension for Google's leveldb:

```lua
require 'leveldb'

local opt = leveldb.options()
opt.createIfMissing = true
opt.errorIfExists = false

local test_key = 'key1'
local test_val = 'value1'

print ('opening test.db')
local testdb = leveldb.open (opt, 'test.db')

if leveldb.check(testdb)
then
	if testdb:put(test_key, test_val)
	then
		print ("Getting test " .. test_key .. " : " .. testdb:get(test_key))
	end
end

leveldb.close(testdb)
```

Iterator Example
-----
```lua
require 'leveldb'

local opt = leveldb.options()
opt.createIfMissing = true
opt.errorIfExists = false

local db = leveldb.open(opt, 'test.db')

db:put('key1', 'value1')
db:put('key2', 'value2')
db:put('key3', 'value3')

local iter = db:iterator()

iter:seekToFirst()

while(iter:valid())
do
	print(iter:key() .. ' ' .. iter:value())
	
	iter:next() -- go next
end
```