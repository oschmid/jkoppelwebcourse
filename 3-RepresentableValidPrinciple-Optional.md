# Optional Minesweeper Design Exercise

See https://gist.github.com/oschmid/49551380cebacc3655c0965c00e785de

# Optional Elasticsearch Exercise

## Noticing bad states
1. CollapseBuilder.build is trying to prevent
	- CollapseContext with scroll context
	- CollapseContext with `search_after`
	- CollapseContext with `rescore`
	- CollapseContext with an unmapped field type
	- CollapseContext with a non-keyword non-number field type
	- CollapseContext with a field type without doc values
	- CollapseContext for inner hits on a non-indexed field
2. CollapseContext can only exist with very specific queries.

## Preventing bad state transitions, redundant data
1. `valid()` checks it's in a valid state and `toValuesSource()` converts it into a ValuesSource subclass (if possible).
2. To wait for at least one of fieldContext, script, or unmapped to be set.
3. Follow a type-safe builder pattern and make the class type `ValuesSourceConfig<VALIDITY>` where the VALID changes from `INVALID` to `VALID` only when one of the above is set. Then make a static method `createValuesSource(ValuesSourceConfig<VALID> config, QueryShardContext context)`. (Valid is a better name in this case than true/false)
4. There's no difference in behaviour for `v.fieldContext ==null or !=null` or `v.script ==null or !=null`.
5. Get rid of the builder setters for fieldContext, script, and umapped and have 3 different subclasses for ValuesSourceConfig. Now we can skip the `valid()` check along with the checks for which case we're in.

## Preventing bad data
1. They must not be NaN or Infinite, top > bottom, and left != right.
2. Some of the validation for longitudes is skipped and instead normalized for in `doToQuery()`.
3. That they're not NaN, or Infinite, and between MIN (-90 for latitude, -180 for longitude) and MAX (90 for latitude, 180 for longitude).
4. If all 360 degrees of longitude are selected, the midpoint defaults to Greenwich.
5. That they're not NaN, or Infinite, and between MIN (-90 for latitude, -180 for longitude) and MAX (90 for latitude, 180 for longitude).
6. `geo.Rectangle` should replace `GeoPoint topLeft` and `GeoPoint bottomRight` in `GeoBoundingBoxQueryBuilder`. Most of the setCorners() methods should be removed and similar code for parsing various inputs into Rectangles created.
