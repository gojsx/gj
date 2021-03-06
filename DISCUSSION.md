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


## Integers

### int64 and uint64

JS has only one number type: IEEE-754 double precision floating point. JS can precisely represent integers plus/minus `2^53-1`

Therefore, (u)int8/16/32 can be represented natively. If we can analyze 64-bit cases and prove that they fall within the 2^53 range, those can use native JS doubles as well.

64-bit integer values outside that range would need to be emulated, for example using "lo" and "hi" variable pairs.

### Flooring division

`x / y | 0` forces flooring division. There may be some better technique.

### Wraparound

asm.js has some techniques for managing integer wraparound behavior (bit ops force integer math). GopherJS has employed these (or similar techniques).

Math.imul is defined as having "C-like 32-bit integer multiplication semantics" and testing in Chrome shows that it wraps around. It specifically seems to use signed integer semantics.


## Time

Go Time values have nanosecond precision with a range of ~292 million millenia, relative to year 1, UTC. JS Date objects have millisecond precision with a range of plus/minus 100,000,000 days (~273 millenia) relative to 1970, UTC.

Time, as representable by JS is therefore about 1 million times less precise, and over a range 1 million times smaller than time representable by Go.

Tasks that involve sleeps or delays could use JavaScript's Date type, and durations converted to milliseconds. Use of time that goes outside the range JS Date range (or that meaningfully needs better than millisecond-precision) may need special code generation.


## Slices

For several common input slice types, JS typed arrays could be used. For example. a []byte in Go may generate code for a Uint8Array in JS.
