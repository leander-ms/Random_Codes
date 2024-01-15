# Random_Codes

Welcome to my GitHub page. 
This page will mostly be about machine learning and python data analysis, but there will also be the occasional Java- or HTML project to keep my memory fresh :)


Public Sub InsertDataFromExcel(RangeToInsert As Range, TableName As String, Optional BatchSize As Long = 100)
    Dim cell As Range
    Dim rowValues As String
    Dim row As Range
    Dim insertQuery As String
    Dim rowCount As Long
    Dim cellValue As String

    rowCount = 0
    insertQuery = "INSERT INTO " & TableName & " VALUES "

    ' Loop through each row in the specified range
    For Each row In RangeToInsert.Rows
        rowCount = rowCount + 1
        rowValues = ""
        
        ' Loop through each cell in the row
        For Each cell In row.Cells
            ' Check if the cell contains a numeric value
            If IsNumeric(cell.Value) Then
                ' Convert the decimal separator from comma to period for floats
                cellValue = Replace(cell.Value, ",", ".")
            Else
                ' For text, replace single quotes and enclose the value in single quotes
                cellValue = "'" & Replace(cell.Value, "'", "''") & "'"
            End If

            ' Add the formatted value to the rowValues string
            rowValues = rowValues & cellValue & ","
        Next cell

        ' Remove the last comma from the rowValues string
        rowValues = Left(rowValues, Len(rowValues) - 1)

        ' Add this row's values to the insert query
        If rowCount = 1 Then
            insertQuery = insertQuery & "(" & rowValues & ")"
        Else
            insertQuery = insertQuery & ",(" & rowValues & ")"
        End If

        ' Execute the query in batches
        If rowCount Mod BatchSize = 0 Then
            conn.Execute insertQuery
            rowCount = 0
            insertQuery = "INSERT INTO " & TableName & " VALUES "
        End If
    Next row

    ' Execute any remaining rows not included in the last batch
    If rowCount > 0 Then
        conn.Execute insertQuery
    End If
End Sub
