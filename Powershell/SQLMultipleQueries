############################################################################
#					SET LOCAL PARAMS 				
############################################################################
# SET VARIABLES
# You have TO SET your SQL Server name NEXT : 
#if you USE a particular instance, the variable should be "$sqlserver = SERVERNAME\INSTANCE"
 
$sqlServer = "localhost" # Example server variable 
$database = "IJCore" # Example OF DB Uses
$query = $("SELECT TOP 5 [dataCalloffID] FROM [IJCore].[dbo].[archivedCalloff]") #Query That need TO be replaced
$query2 = $("SELECT TOP 2 [dataCalloffAdditionalInteger1] FROM [IJCore].[dbo].[archivedCalloff]") #Query That need TO be replaced
$auth = "IS" #The other mode IS "IS", IF you want TO USE Windows integrated credentials CHANGE "BDDUSER" BY "IS"
$uid = "User" # Edit TO your specific USER
$pwd = "Password" #Edit TO your specific
 
############################################################################
#					NO TOUCHING			
############################################################################
 
FUNCTION printout_errors($error_code, [string]$message){
	#Error Message Printing
    $ErrorMessage = $_.Exception.Message
    $FailedItem = $_.Exception.ItemName
	Write-Host "$error_code $message $ErrorMessage, $FailedItem"
 
}
 
#Connection Method Test
IF ($auth -eq "BDDUSER") {
	$connString = "Server=$sqlServer;Database=$database;User Id=$uid;Password=$pwd;"       
    }
ELSE{
    $connString = "Server= $sqlServer;Database=$database;Integrated Security=True;"
}
#Connect
Try{
    $sqlConn = New-Object System.Data.SQLClient.SQLConnection
    $sqlConn.ConnectionString = $connString
    $sqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $sqlCmd.Connection = $sqlConn
    $sqlConn.Open()
 
}
Catch{   
    printout_errors(3,"Connection Problems")
    exit
}
#Query
Try{
    #Command One
    $sqlCmd.CommandText = $query
    $sqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $sqlAdapter.SelectCommand = $sqlCmd
    $dataSet = New-Object System.Data.DataSet
    $sqlFill = $sqlAdapter.Fill($dataSet)
    #Liberation OF the LAST Query
    $sqlCmd.Dispose()
    }
Catch{
    printout_errors(2,"MSSQL query failed for the following reason : ")
}
Try {
    #Second Query
    $sqlCmd.CommandText = $query2
    $sqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $sqlAdapter.SelectCommand = $sqlCmd
    $dataSet2 = New-Object System.Data.DataSet
    $sqlFill2 =$sqlAdapter.Fill($dataSet2)
    }
Catch{
    printout_errors(2,"MSSQL query_2 failed for the following reason : ")
}
#In any CASE, close the connection
Finally{
    $sqlconn.Close()
}
############################################################################
#					DEBUG			
############################################################################
#If you want TO know what results you GET FROM your raw SQL query, uncomment the following block
<#Try{   
    #$dataSet.Tables[0].Rows | Select-Object * -ExcludeProperty ItemArray, TABLE, RowError, RowState, HasErrors | ConvertTo-Json
    #$dataSet2.Tables[0].Rows | Select-Object * -ExcludeProperty ItemArray, TABLE, RowError, RowState, HasErrors | ConvertTo-Json
}
Catch{
    printout_errors(3,"ARRAY Problem(s)")
    exit
}#>
############################################################################
#					EDIT		
############################################################################
 
Try{
	#Do anything WITH your RESULT
	#In this CASE, $sqlFill IS a COUNT ON the rownumber you selected ON request
	#Example which COUNT ROWS AND define Statement ON this NUMBER
    IF($sqlFill -gt 1) {
        $result = $dataSet.Tables[0].Rows.Count
        Write-Host "0 MSSQL Query OK - $result Rows is OK"
        }
    elseif($sqlFill -eq 1){
        $result = $dataSet.Tables[0].Rows.Count
        Write-Host "2 MSSQL Query Warning - $result Rows is Warning"
    }
    ELSE{
        $result = $dataSet.Tables[0].Rows.Count
        Write-Host "2 MSSQL Query Critical - $result Rows is Crticial"
    }
    #Maybe ADD SOME FUNCTION TO make Specifics checks  AND validate the query
}
Catch{
 	printout_errors(2,"Custom handling of SQL result failed for the following reason : ")
}


