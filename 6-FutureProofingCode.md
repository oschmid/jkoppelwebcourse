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

1. TODO
1. You need a version flag that says whether to treat it the old way (no semitransparency) or the new way (with semitransparency) and leave room for future versions with say a larger range of transparency values for example.
1. You'd expect for those with transparency values <50% to be fully transparent and those with values >=50% to be fully opaque.
1. TODO

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
