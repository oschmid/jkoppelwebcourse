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

1. The image header has a `property-list` datastructure in which to store new properties. This makes the format open in the set of properties. Properties also need a type field to make them identifiable.
1.
   1. PROP_COLORMAP is not open in the list of fields. There is no version number. To add new fields would require a new field type PROP_COLORMAP_WITH_VERSION with a version field (or named fields) that would be open to further field additions or format changes.
   1. PROP_COLOR is not open in the list of fields. That's why it was replaced by a new type PROP_FLOAT_COLOR. Since this new property also lacks a version number (or named fields) it took will need to be replaced should it need changing.


### Complexity Ratchets

1. 
   1. ```(string, uint32, byte, uint32, uint32, 1, List (0 + 1, int32, int32))```
   1. ```(string, uint32, byte, uint32, uint32, 2, 1, List(0 + 1, float, float))```
   1. ```(string, uint32, byte, uint32, uint32, 3, 1, uint32, List(0 + 1, float, float))```
1. 3 is a subtype of 2. The only difference is the version (and any functionality related to the tattoo field which wouldn't be used).
1. 
   ```
   (string, uint32, byte, uint32, uint32, uint32, List(0 + 1))
   
   destructor:
     delete string name
     delete uint32 linked
     delete byte state
     delete uint32 closed
     delete uint32 np
     delete uint32 version
     for (i = 0; i < np; i++) {
        delete int32 type
     }
   ```
1. The non-existence of keys for each property makes the only identifiable difference the version number which forces if statements.
1. A map of named properties:
   ```
   "name": string
   "linked": uint32
   "state": byte
   "closed": uint32
   "np": uint32
   "version": uint32
   "dummy": uint32 (version >=2)
   "tattoo": uint32 (version >=3)
   "points": List of:
      "type": int32
      "x": int32 (version <=1)
      "y": int32 (version <=1)
      "x_float": float (version >=2)
      "y_float": float (version >=2)
   ```
   For formats 2 and 3 to be a subtype of 1 they would have to round x and y coordinates to the nearest integer. Pre-1999 GIMP could then skip properties of newer formats and new GIMP could convert x and y coordinates from int to float based on name.

#### Bonus

1. Since it has a version field new fields can be added without breaking forward-compatibility. However fields can't be removed or changed to different types. TODO
1. If individual paths need versions, the whole property probably needs versions.
