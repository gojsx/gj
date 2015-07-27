# Go stdlib -> JS stdlib shims

## sort

JS `Array.prototype.sort` is both unstable and in-place, which makes it semantically compatible with sort.Sort when the implementation is a slice.

The Go Less method can be used to generate a JS cmp function.

### Tertiary expression approach

Less(i, j) and Less(j, i) can be semantically extracted to generate a JS tertiary expression.

	/* ... other sort methods... */
	func (t T) Less(i, j int) bool { t[i].x < t[j].x }

	sort.Sort(t)

Becomes:

	t.sort(function(a, b) { return a.x < b.x ? -1 : (b.x < a.x ? 1 : 0); });

### First-order function approach

Alternatively, a first-order function can be used for selecting values to compare.

	/* ... other sort methods... */
	func (t T) Less(i, j int) bool { t[i].x < t[j].x }

	sort.Sort(t)

Becomes:

	//mkcmp will be defined once and reused.
	function mkcmp(selfun) {
		return function(a, b) {
			a = selfun(a);
			b = selfun(b);
			return a < b ? -1 : (b < a ? 1 : 0);
		};
	}

	// this line will be generated.
	t.sort(mkcmp(function(v) { return v.x }));

## math

Many math package functions are semantically shared between Go's math package and the JS stdlib. Use of Go's math functions will generate direct uses of JS's Math functions, where appropriate.
