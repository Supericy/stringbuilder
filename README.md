# StringBuilder.php
<h2>StringBuilders, not happening in PHP</h2>

This class and accompanying files attempts to demonstrate that a StringBuilder 
in PHP doesn't make sense at all, and in fact would have a detrimental effect on
the performance of a PHP program. The reason being is that variables in PHP are 
modifiable (mutable) meaning we can modify the string where it sits in memory. I 
used as many speed tricks as I could to squeeze as much out of it as possible - 
if you can see something I missed that would improve the speed, please, let me 
know.

<h2>What is a String Builder?</h2>
When concatenating a string in a language such as C/C++ or Java, a new memory 
area has to be found and allocated to fit the original string - plus the string 
being added. In C, this is done by the programmer using malloc/remalloc. In 
languages like Java or C++ (using std::string) this is done automatically. You 
could guess, that concatenating a string in a loop could take a lot longer 
than it should with all of the reallocation and coping of memory.

A String Builder is quite simple, it preallocates a large block of memory so 
that when more data is added, it doesn't have to reallocate memory and copy 
strings around. For explanation purposes, lets say a string builder preallocates 
20 bytes of empty space: (periods are used for string length visibility)

```php
$x = '....................'; 
$xlen = 0; // the real length of our string - it starts at 0 of course
```

If we call the String Builders append method with the text 'x1234' it will 
'insert' (not 'copy and append') the string value at position '0', add the 
length of the string to a variable so we know how large our string is:

```php
$x = 'x1234...............';
$xlen = $xlen + strlen('x1234'); // the current length of our string is now 5
```

If we called the appender again with '5678' it would insert the value at 
position $xlen (which is 5). 

```php
$x = 'x12345678...........';
$xlen = $xlen + strlen('5678'); // the current length of our string is now 9
```

When we want the value of the string, a method like .toString() or .str() 
can be called that would basically return the substring of the string buffer. In
our example, it would do something like this: 

```php
$x = 'x12345678...........'; //
return substr(buffer, 0, $xlen); // which of course returns 'x12345678'. 
```

As you can see, the original string is much larger than 9, but we only return
what we inserted by specifying the string, start position (0) and the virtual
length of the appended string.

<h2>Conclusion</h2>
Although a string builder can be optimal, it can also be a total bust. There are 
likely more reasons to just use straight concatenation than a String Builder. 
When a string is returned using .toString() or .str() it actually creates a copy
of the string before returning it to the caller. String Builders also use a lot 
more memory than immutable string manipulation would, as the clear source of the 
speed comes from preallocating a block of memory rather than allocating exactly
what it needs, and no more. The preallocation saves time by not having to call 
malloc/realloc every append.

<h2>Discovery!</h2>
Was this all a waste of time? I don't think so, while trying to squeeze every 
bit of juice out of the StringBuilder I came across something that I'll be write
about next, which will out-perform PHP's internal string concatenation! 

Want a Hint? 
StringStream.php