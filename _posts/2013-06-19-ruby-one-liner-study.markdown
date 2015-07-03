---
layout: post
title:  "Ruby one-liner study"
date:   2013-06-19 00:00:00
---
In that [presentation][presentation], there is a funny ruby one-liner. Here is the explanation:

    yes|ruby -ne '$><<"\r\x1b[#{31+($.+=1)%7}mZOMG PREFORKING"; sleep 0.1;'

How does it work?

The `-n` option means `assume 'while gets(); ... end' loop around your script`. `$>` is the default output stream (kind of an alias of `$stdout`) and the `<<` operator of the `IO` class writes an object to a stream.

So basically each time the program receives an input (with the `yes` command), it outputs the `"\r\x1b[#{31+($.+=1)%7}mZOMG PREFORKING"` string and sleeps 100 milliseconds.

So now, what does that weird string mean?

The `\r` character is the carriage return so that the program write the string at the beginning of the line and erase what was previously written.

The `\x1b` character is the escape character. It is the same as `\033` and `\e` and corresponds to the 27th character in the ASCII table. The character which is escaped is [ and that means that here we start a sequence of non-printing characters. In our case, the sequence will define some foreground colour.

`$.` is a special variable which contains the line number last read by interpreter. As a small example, the following command will print the number of time you pressed the `<Enter>` key each time you press it:

    ruby -ne '$> << $.'

The value computed by the expression `31+($.+=1)%7` will always be between 31 and 37.

Maybe one day I’ll finish this explanation.

[presentation]: http://youtu.be/2rAoasxZKGU?t=8m4s
