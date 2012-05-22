---
layout: post
title: Using Redis SORT and GET to save on roundtrips 
categories:
    - programming
    - redis
---
A common pattern when storing more complex data in [Redis](http://redis.io) is to use plain strings or hashes to store a representation of some data object,
and then store references to those keys in Redis' other data structures (lists, sets, and sorted sets).  A contrived example might look like this:

    redis> LRANGE FooBar|list 0 -1
    1) "1"
    2) "2"
    3) "4"

    redis> GET FooBar|id|1
    "One"

    redis> GET FooBar|id|2
    "Two"

    redis> GET FooBar|id|4
    "Four"

    redis>

A common access pattern for this type of data seems to be to first retrieve the ids contained in the list, and then retrieve the keys one by one
as shown in the example above.  The primary deficiency of this approach is that it requires a network operation for each item referenced in the list.
I'm a fan of trying to retrieve data with as few I/Os as possible.

If you were to use a [MULTI/EXEC](http://redis.io/topics/transactions) transaction for the GETs you could retrieve the contents in two operations, but one oft overlooked feature of the
[SORT](http://redis.io/commands/sort) command will let you retrieve the entire structure in a single I/O: GET.

Using SORT and GET is fairly straightforward.  We can point the SORT command at lists and sorted sets and retrieve the contents in specified order.
If we use the GET clause, we can specify a pattern for keys external to the list that we would like to retrieve instead of the sorted list contents.  So
now we can finally dereference our references!

Using the contrived example above, the magic command would be:

    redis> SORT FooBar|list GET FooBar|id|*
    1) "One"
    2) "Two"
    3) "Four"

    redis>

Nice! However, what if you want to retrieve the contents of the list in the order they were inserted and bypass the sorting?  Redis provides a way to
do that by sorting on a nonexistent key (have a look at the SORT ... BY syntax for more details):

    redis> SORT FooBar|list BY nonexistentkey GET FooBar|id|*

Hopefully someone else finds this as useful as I do.  Sometimes it pays to peruse the docs! 

