# Documentation jQuery by Arip budiman

```
Sub PushDataDroping()
Dim Data As String
Data = Sheets("Pencairan").Range("B5:T2000").Copy
Workbooks.Open ("D:\ROUTE\FORMAT LAPORAN\FORMAT PRINT LAPORAN BULANAN")
Sheets("Droping").Range("A2").PasteSpecial xlPasteValues
End Sub
```
