# Execute external database Stored Procedure from X++ code using ODBC connectivity


This is to show a method to execute external database stored procedures from X++ code. Create a job in AOT with following code. Follow the steps as explained here in the code like replace `Server Name`, `Database name` , `Stored Procedure name`.

The below code is executed through `the ODBC Connection`.

```C#

static void execExternalDatabase(Args _args)
{
    LoginProperty loginProperty;
    ODBCConnection odbcConnection;
    Statement statement;
    ResultSet resultSet;
   
	ResultSetMetaData resultSetMetaData;
    Counter counter;
    str sql;
    SqlStatementExecutePermission perm;
    ;
 
   
    loginProperty = new LoginProperty();
    loginProperty.setServer("SERVERNAME Here"); // Replace your Database Server Name here
    loginProperty.setDatabase("DemoDB"); //Replace your Database name here
    odbcConnection = new ODBCConnection(loginProperty); // setting odbc connection here.
 
    // ODBC Connection to create statement
    statement = odbcConnection.createStatement();
 
    // Replace the StoredProcedure you want to execute.
    sql = strfmt('EXEC[myStoredProcedureName]');
 
    // Set code access permission to Execute
    perm = new SqlStatementExecutePermission(sql);
    perm.assert();
 
    try
    {
        // if Stored Procedure has Select query use executeQuery method.
       
        resultSet = statement.executeQuery(sql);
        resultSet.next();
       
        resultSetMetaData = resultSet.getMetaData();
        for (counter = 1; counter <= resultSetMetaData.getColumnCount(); counter++)
        {
            switch(resultSetMetaData.getColumnType(counter))
            {
                case 0,1 :
                    info(resultSet.getString(counter));
                break;
                case 3:
                    info(date2StrUsr(resultSet.getdate(counter)));
                break;
            }
        }
    }
    catch (exception::Error)
    {
        print "An error occured in the query.";
        pause;
    }
    // Code access permission scope ends here.
    CodeAccessPermission::revertAssert();
}

```

Used a simple select query in the stored procedure and the result will be displayed on the infolog.

