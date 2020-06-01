## Readings

### Designing Software for Ease of Extension and Contraction - David Parnas

TODO

### Martian Headsets - Joel Spoelsky

If you're writing something new don't "be liberal in what you accept". It'll bite you in the long run. If you're updating something make sure to keep it backwards compatible or update all the callers of your function.

### Gay Marriage: The Database Engineering Perspective - qntm

TODO

### Optional: Why are the Microsoft API Formats So Complicated - Joel Spoelsky

It would have greatly reduced the size and complexity of the serialization and deserialization code. But it would have slowed down those same processes on the machines at the time.

## Case Study: GIMP File Format

### Assumptions

1. It looks like they got locked in to the choice of `gdouble` to represent opacity rather than a `gboolean`. That may have let them reused the code for indexing RGB colors to also index alphas. But it broke the 1-to-1 representational relationship between a boolean transparent/opaque design and the actual data type.
1. You need a version flag that says whether to treat it the old way (no semitransparency) or the new way (with semitransparency) and leave room for future versions with say a larger range of transparency values for example.
1. You'd expect for those with transparency values <50% to be fully transparent and those with values >=50% to be fully opaque.
1. When semitransparency was not supported the file format should have stored it as a boolean transparent/opaque rather than a byte with 256 different possible values. This would have forced a newer file version to use a different property key and value type to store semitransparent ranges.

### Openness

1. TODO
1. TODO

### Complexity Ratchets

1. TODO
1. TODO
1. TODO
1. TODO
1. TODO

#### Bonus

1. TODO
1. TODO
