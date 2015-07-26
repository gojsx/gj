# Open Questions

## Byte values vs Unicode codepoints

JS strings are a sequence of codepoints, represented internally as either UCS-2 or UTF-16 (implementation choice). Several aspects of JS string handling are treated as "UCS-2 with surrogates."

Go strings are a sequence of bytes, _conventionally_ UTF-8 encoded.

### Easy cases

- string is used in a range loop, with the index discarded.

- string element ("byte") is compared against an ASCII value.

### Manageable cases

- string slicing/indexing always occurs on codepoint boundaries (with constant input) OR all codepoints in string provably the same UTF-8 encoded length (such as pure-ASCII).


## Name visibility transformation

Should names be transformed to match JS style?

### fmt.Stringer

Investigate transforming `String() string` into JS toString method. See section on name visibility.


## Pointers


