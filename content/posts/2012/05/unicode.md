Title: Напоминание: использование unicode
Author: d.rey
Date: 2012-05-28 09:50:00
Slug: unicode
Tags: hint,unicode,python

Кажется прописные истины, но время от времени на них натыкаешься.

The most important tip is: Software should only work with Unicode strings internally, 
converting to a particular encoding on output.

If you attempt to write processing functions that accept both Unicode and 8-bit strings, 
you will find your program vulnerable to bugs wherever you combine the two different kinds of strings.

[Tips for writing Unicode-aware Programs](http://docs.python.org/howto/unicode.html#tips-for-writing-unicode-aware-programs)

