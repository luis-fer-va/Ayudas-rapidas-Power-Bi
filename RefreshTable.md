```
let
Source = Duration.TotalSeconds(DateTime.LocalNow() - #datetime(1970,1,1,0,0,0)),
Source1 = Source1,
Source2 = Source2,

  // add more sources for each table you want to refresh

  #"Converted to Table" = #table(1, {{Source}}),
  #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "RefreshTime"}}),
  #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"RefreshTime", Int64.Type}})

in
  #"Changed Type"
```
