---
title: What the date?
date: 2023-11-13
---

A friend of mine found out something interesting with the `date` command in
coreutils:

> Are these different for you too?
```bash
$ date -d 'a week ago'
$ date -d '1 week ago'
```

Indeed, after trying it on my terminal, the two dates differ by one hour.

```bash
$ date -d 'a week ago'
Mon  6 Nov 21:51:16 GMT 2023
$ date -d '1 week ago'
Mon  6 Nov 22:51:18 GMT 2023
```

However, the results are different for my friend:

> For me it's off by 6h, another machine off by 4h.

This got my curious and I started to dig into the manual[^manual]. First of all, `-d`
flag allows the user to specify a time:

>        -d, --date=STRING
>             display time described by STRING, not ’now’

Let's see what such a `STRING` could be:

>     DATE STRING
>            The  ‐‐date=STRING is a mostly free format human readable date
>            string such as "Sun, 29 Feb 2004 16:21:42 ‐0800" or "2004‐02‐29
>            16:21:42" or even "next Thursday". A date string may contain items
>            indicating calendar date, time of day, time zone, day of week,
>            relative time, relative date, and numbers. An empty string
>            indicates the beginning of the day. The date string format is more
>            complex than is easily documented here but is fully described in
>            the info documentation.

Okay, let's go through the [date input formats
documentation](https://www.gnu.org/software/coreutils/manual/html_node/Date-input-formats.html)
to see how it works.

Under Section 29.7 "[Relative items in date
strings](https://www.gnu.org/software/coreutils/manual/html_node/Relative-items-in-date-strings.html)":

> The unit of time may be preceded by a multiplier, given as an optionally
> signed number. Unsigned numbers are taken as positively signed. No number at
> all implies 1 for a multiplier. Following a relative item by the string ‘ago’
> is equivalent to preceding the unit by a multiplier with value -1.

The examples under this section all use numeric multipliers, such as "1 year"
or "1 year ago", instead of "a year" or "a year ago". It appears that "a"
is recognised incorrectly as part of the input, but what is it?

At this point I decided to dive into the source code for parsing the date
string, which is located [here](https://github.com/coreutils/gnulib/blob/master/lib/parse-datetime.y).
I looked for what can possible accepting the input "a", and I found a
[candidate](https://github.com/coreutils/gnulib/blob/c165047d204745a97869113b75f43cbee761c8b7/lib/parse-datetime.y#L1165)
for the letter "a":

```c
static table const military_table[] =
{
  { "A", tZONE,  HOUR ( 1) },
  ...
};
```

So the letter "a" stands for the [military
timezone](https://en.wikipedia.org/wiki/Military_time_zone) "alfa" (GMT+1),
which explains the difference in output:
I am currently in timezone GMT; "week ago" implicitly means "1 week ago" and
the timezone alfa ("a week ago") would be 1 hour before the "1 week ago" in my
current timezone, thus the difference.
My friend's computer is possibly in a different timezone, thus resulting in a
difference gap.

That was a interesting finding. Had we been in the summer time (GMT+1), "a
week ago" and "1 week ago" would actually result in the same output, and we
would never realise the problem.

Take away message: when specifying a relative time using `date`, don't use the
indefinite article "a" and always use the numeric "1".

[^manual]: The version of coreutils on my Fedora 39 is 9.3.
