############################################################################
#					SET LOCAL PARAMS 				
############################################################################
 
set-executionpolicy RemoteSigned
 
# SET VARIABLES
# You have TO SET your SQL Server name NEXT : 
#if you USE a particular instance, the variable should be "$sqlserver = SERVERNAME\INSTANCE"
 
##################################################################
#DONT ADD SPACE IN the STRING IF you want TO CHANGE Service_name
$service_name = "MSSQL_CONNECTIONS"
##################################################################
 
$sqlServer = "localhost" # Example server variable 
$database = "master" # Example OF DB Uses
$query = $("SELECT TOP 10 [connections] FROM [master].[dbo].[spt_monitor]") #Query That need TO be replaced
$auth = "BDDUSER" #The other mode IS "IS", IF you want TO USE Windows integrated credentials CHANGE "BDDUSER" BY "IS"
$uid = "User" # Edit TO your specific USER
$pwd = "Password" #Edit TO your specific
 
############################################################################
#					NO TOUCHING			
############################################################################
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
	Write-Host "3 $service_name - "$_.Exception.Message
    exit
}
#Query
Try{
    #Command One
    $sqlCmd.CommandText = $query
    $sqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $sqlAdapter.SelectCommand = $sqlCmd
    #Fill SQL adapter WITH the dataset AND affect it TO a sqlFill Variable
    $dataSet = New-Object System.Data.DataSet
    $sqlFill = $sqlAdapter.Fill($dataSet)
    $result = $dataSet.Tables[0].Rows.connections
    #Liberation OF the LAST Query
    $sqlCmd.Dispose()
    }
Catch{
    Write-Host "3 $service_name - "$_.Exception.Message
}
#In any CASE, close the connection
Finally{
    $sqlconn.Close()
}
############################################################################
#					EDIT		
############################################################################
#We exit ALL TIME WITH this STATUS. here, 
#if connections are less than 15 its ok 
#Between 15 AND 50 its Warning
#More than 50 its critical
# 0 = OK
# 1 = WARNING
# 2 = CRITICAL
# 3 = UNKNOWN
 
[INT]$crit_threshold = 50
[INT]$warn_threshold = 2
 
 
Try{
	#Do anything WITH your RESULT
	#In this CASE, We CHECK the total connection NUMBER (that thresholds are just examples)
    IF($result -gt $crit_threshold)
        {Write-Host "2 $service_name - $result connections (CRIT above "$crit_threshold")"}
    elseif($result -gt $warn_threshold)
        {Write-Host "1 $service_name - $result connections (WARN between "$warn_threshold" and "$crit_threshold")"}
    ELSE
        {Write-Host "0 $service_name - $result connections (OK above 0 and below "$warn_threshold")"}
    #Maybe ADD SOME FUNCTION TO make Specifics checks  AND validate the query
}
Catch{
    #We exit WITH UNKNOWN alert IN CASE something goes wrong WITH thresholding
 	Write-Host "3 $service_name - "$_.Exception.Message
}
############################################################################
#					DEBUG			
############################################################################
#If you want TO know what results you GET FROM your raw SQL query, uncomment the following block
<#Try{   
    #$dataSet.Tables[0].Rows | Select-Object * -ExcludeProperty ItemArray, TABLE, RowError, RowState, HasErrors | ConvertTo-Json
    #$dataSet2.Tables[0].Rows | Select-Object * -ExcludeProperty ItemArray, TABLE, RowError, RowState, HasErrors | ConvertTo-Json
}
#Catch{
	#Write-Host "3 ARRAY Problem(s) : "$_.Exception.Message
    #exit
}#>
