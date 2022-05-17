[![Actions Status](https://github.com/raku-community-modules/IRC-Utils/actions/workflows/test.yml/badge.svg)](https://github.com/raku-community-modules/IRC-Utils/actions)

NAME
====

IRC::Utils - handy IRC utilities for use in other IRC-related modules

SYNOPSIS
========

```raku
use IRC::Utils;

my Str $nick    = '[foo]^BAR^[baz]';
my Str $uc_nick = uc_irc($nick);
my Str $lc_nick = lc_irc($nick);

# Check equivalence of two nicknames
if eq_irc($uc_nick, $lc_nick) {
    say "These nicknames are the same!";
}

# Check if nickname conforms to RFC1459
if is_valid_nick_name($nick) {
    say "Nickname is valid!";
}
```

DESCRIPTION
===========

The `IRC::Utils` module provides a procedural interface for performing many common IRC-related tasks such as comparing nicknames, changing user modes, normalizing ban masks, etc. It is meant to be used as a base module for creating other IRC-related modules.

Internet Relay Chat (IRC) is a teleconferencing system used for real-time Internet chatting and conferencing. It is primarily used for group communication but also allows private one-to-one messages. The protocol is published in RFC 1459 <https://www.rfc-editor.org/rfc/rfc1459.txt>.

SUBROUTINES
===========

Unlike the `IRC::Utils` module for Perl 5, you do not need to specify the `:ALL` tag when importing the module. Therefore, the following subroutines are exported into the caller's namespace by default.

uc_irc(Str $value, Str $type?)
------------------------------

Converts a string to uppercase that conforms to the allowable characters as defined by RFC 1459.

The `$value` parameter represents the string to convert to uppercase.

The `$type` parameter is an optional string that represents `$value`'s casemapping. It can be 'rfc1459', 'strict-rfc1459', or 'ascii'. If not given, it defaults to 'rfc1459'.

Returns the value of `$value` converted to uppercase according to `$type`.

Example:

```raku
my Str $uc_hello = uc_irc('Hello world!');

say $hello;
# Output: HELLO WORLD!
```

-head2 lc_irc(Str $value, Str $type?)

Converts a string to lowercase that conforms to the allowable characters as defined by RFC 1459.

The `$value` parameter represents the string to convert to lowercase.

The `$type` parameter is an optional string that represents `$value`'s casemapping. It can be 'rfc1459', 'strict-rfc1459', or 'ascii'. If not given, it defaults to 'rfc1459'.

Returns the value of `$value` converted to lowercase according to `$type`.

Example:

```raku
my Str $lc_hello = lc_irc('HELLO WORLD!');

say $lc_irc;
# Output: hello world!
```

eq_irc(Str $first, Str $second, Str $type?)
-------------------------------------------

Checks the equivalence of two strings that conform to the allowable characters as defined by RFC 1459.

The `$first` parameter is the first string to compare.

The `$second` parameter is the second string to compare.

The `$type` parameter is an optional string that represents the casemapping of `$first` and `$second`. It can be 'rfc1459', 'strict-rfc1459', or 'ascii'. If not given, it defaults to 'rfc1459'.

Returns `Bool::True` if the two strings are equivalent and `Bool::False` otherwise.

Example:

```raku
my Str  $upper = '[F00~B4R~B4Z]';
my Str  $lower = '{f00~b4r~b4z}';
my Bool $equal =  eq_irc();

say 'They're equal!' if $equal;
# Output: They're equal!
```

parse_mode_line(@mode)
----------------------

Parses a list representing an IRC status mode line.

The `@mode` parameter is an array representing the status mode line to parse. You may also pass an array or hash to specify valid channel and status modes. If not given, the valid channel modes default to `<beI k l imnpstaqr> ` and the valid status modes default to `{o => '@', h => '%', v => '+'} `.

Returns a hash containing two keys: `modes` and `args`. The `modes` key is an array of normalized modes. The `args` key is an array that represents the relevant arguments to the modes in `modes`.

If for any reason the mode line in `@mode` can not be parsed, a `Nil` hash will be returned.

Example:

```raku
my %hash = parse_mode_line(<ov foo bar>);

say %hash<modes>[0];  # +o

say %hash<modes>[1];  # +v

say %hash<args>[0];   # foo

say %hash<args>[1];   # bar
```

normalize_mask(Str $mask)
-------------------------

Fully qualifies or "normalizes" an IRC host/server mask.

The `$mask` argument is a string representing a host/server mask.

Returns `$mask` as a fully qualified mask.

Example:

```raku
    my Str $mask = normalize_mask('*@*');

    say $mask;
    # Output: *!*@*
```

numeric_to_name(Int $code)
--------------------------

Converts an IRC reply or error code to its corresponding string representation. This includes all values defined by RFC 1459 and also a few network-specific extensions.

The `$code` parameter is an integer representing the numeric code to convert. For instance, 461 which is `ERR_NEEDMOREPARAMS`.

Returns the string representation of `$code`.

Example:

```raku
my Str $topic = numeric_to_name(332);

say $topic;
# Output: RPL_TOPIC
```

name_to_numeric(Str $name)
--------------------------

Converts a string representation of an IRC reply or error code into its corresponding numeric code. This includes all values defined by RFC 1459 and also a few network-specific extensions.

The `$name` parameter is a string representing the reply or error code. For instance, `ERR_NEEDMOREPARAMS` is 461.

Returns the numerical representation of `$name`.

Example:

```raku
my Int $topic name_to_numeric('RPL_TOPIC');

say $topic;
# Output: 332
```

is_valid_nick_name(Str $nick)
-----------------------------

Checks if an IRC nickname is valid. That is, it conforms to the allowable characters defined in RFC 1459.

The `$nick` parameter is a string representing the nickname to validate.

Returns `Bool::True` if `$nick` is a valid IRC nickname and `Bool::False` otherwise.

Example:

```raku
my Bool $valid_nick = is_valid_nick_name('{foo_bar_baz}');

say 'Nickname is valid!' if $valid_nick;
# Output: Nickname is valid!
```

is_valid_chan_name(Str $chan, Str @types?)
------------------------------------------

Checks if an IRC channel name is valid. That is, it conforms to the allowable characters defined in RFC 1459.

The `$chan` parameter is a string representing the channel name to check.

The `@types` parameter is an optional anonymous list of channel types. For instance, '#'. If not given, it defaults to `['#', '&']`.

Returns `Bool::True` if `$nick` is a valid IRC channel name and `Bool::False` otherwise.

Example:

```raku
my Bool $valid_chan = is_valid_chan_name('#foobar');

say 'Channel name is valid!' if $valid_chan;
# Output: Channel name is valid!
```

unparse_mode_line(Str $line)
----------------------------

Condenses or "unparses" an IRC mode line.

The `$line` parameter is a string representing an arbitrary number of mode changes.

Returns the condensed version of `$line` as a string.

Example:

```raku
my Str $mode = unparse_mode_line('+m+m+m-i+i');

say $mode;
# Output: +mmm-i+i
```

gen_mode_change(Str $before, Str $after)
----------------------------------------

Determines the changes made between two IRC user modes.

The `$before` parameter is a string representing the user mode before the change.

The `$after` parameter is a string representing the user mode after the change.

Returns a string representing the modes that changed between `$before` and `$after`. That is, any modes that were added or removed from `$before` to create `$after`.

Example:

```raku
my Str $mode_change = gen_mode_change('abcde', 'befmZ');

say $mode_change;
# Output: -acd+fmZ
```

matches_mask(Str $mask, Str $match, Str $mapping?)
--------------------------------------------------

Determines whether a particular user/server matches an IRC mask.

The `$mask` parameter is a string representing the IRC mask to match against.

The `$match` parameter is a string representing the user/server to check.

The `$mapping` parameter is an optional string that specifies which casemapping to use for `$mask` and `$match`. It can be 'rfc1459', 'strict-rfc1459', or 'ascii'. If not given, it defaults to 'rfc1459'.

Example:

```raku
my Str  $banmask = 'foobar*!*@*';
my Str  $user    = 'foobar!baz@qux.net';
my Bool $matches = matches_mask($banmask, $user);

say "The user $user is banned" if $matches;
# Output: The user foobar!baz@qux.net is banned
```

parse_user(Str $user)
---------------------

Parses a fully-qualified IRC username and splits it into the parts representing the nickname, username, and hostname.

The `$user` parameter is a string representing the fully-qualified username to parse. It must be of the form `nick!user@host`.

Returns a list containing the nickname, username, and hostname parts of `$user`.

Example:

```raku
my Str ($nick, $user, $host) = parse_user('foo!bar@baz.net');

say $nick
# Output: foo

say $user
# Output: bar

say $host
# Output: baz.net
```

has_color(Str $string)
----------------------

Checks if a string contains any embedded color codes.

The `$string` parameter is the string to check.

Returns `Bool::True` if `$string` contains any embedded color codes and `Bool::False` otherwise.

Example:

```raku
my Bool $color = has_color("\x0304,05This is a colored message\x03");

say 'Oh, pretty colors!' if $color;
# Output: Oh, pretty colors!
```

has_formatting(Str $string)
---------------------------

Checks if a string contains any embedded text formatting codes.

The `$string` parameter is the string to check.

Returns `Bool::True` if `$string` contains any embedded formatting codes and `Bool::False` otherwise.

Example:

```raku
my Bool $fmt_text = has_formatting("This message has \x1funderlined\x0f text");

say 'I got some formatted text!' if $fmt_text;
# Output: I got some formatted text!
```

strip_color(Str $string)
------------------------

Strips a string of all embedded color codes (if any).

The `$string` parameter is the string to strip.

Returns the string given in `$string` with all embedded color codes removed. If the given string does not contain any color codes, the original string is returned as is.

Example:

```raku
my Str $stripped = strip_color("\x03,05Look at the pretty colors!\x03");

say $stripped;
# Output: Look at the pretty colors!
```

strip_formatting(Str $string)
-----------------------------

Strips a string of all embedded text formatting codes (if any).

The `$string` parameter is the string to strip.

Returns the string given in `$string` with all embedded text formatting codes removed. If the given string does not contain any text formatting codes, the original string is returned as is.

Example:

```raku
my Str $stripped = strip_formatting('This is \x02strong\x0f!");

say $stripped;
# Output: This is strong!
```

CONSTANTS
=========

The following constants are provided to embed color and formatting codes in IRC messages.

Normal text
-----------

    NORMAL

Formatting
----------

    BOLD
    UNDERLINE
    REVERSE
    ITALIC
    FIXED

Colors
------

    WHITE
    BLACK
    BLUE
    GREEN
    RED
    BROWN
    PURPLE
    ORANGE
    YELLOW
    LIGHT_GREEN
    TEAL
    LIGHT_CYAN
    LIGHT_BLUE
    PINK
    GREY
    LIGHT_GREY

To terminate a single formatting code, you must use its respective constant. Additionally, you may use the `NORMAL` constant to terminate *all* formatting codes (including colors).

Conversely, a single color code must be terminated with the `NORMAL` constant. However, this has the side effect of also terminating any other formatting codes.

Example:

```raku
my Str $foo = 'Oh hai! I haz ' ~ GREEN ~ 'green ' ~ NORMAL ~ 'text!';
my Str $bar = BOLD ~ UNDERLINE ~ 'K thx bye!' ~ NORMAL;
```

AUTHOR
======

Kevin Polulak

COPYRIGHT AND LICENSE
=====================

Copyright 2011 Kevin Polulak

Copyright 2012 - 2022 Raku Community

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

