# Execute Stored Procedure from X++ code


To execute a stored procedure from X++ use the Server method, the Client method does not have permissions. you don’t require any special privileges or permissions to execute a stored procedure. if used other then Server method, a message should appear like this `Request for the permission of type SqlStatementExecutePermission failed.`

```C#
public static void main(AssemblyLoadEventArgs _args)
{
    Connection con = new Connection();
    Statement stmt = new Con.createStatement();
 
    ResultSet r;
    str sql;
 
    SqlStatementExecutePermission perm;
    sql = stmt('EXEC [StoreprocedureName]');
    perm = new SqlStatementExecutePermission(sql);
    perm.assert();
    try
    {
        stmt.executeUpdate(sql);
    }
    catch (Exception::Error)
    {
        print "An error occured in the query";
        pause;
    }   
    CodeAccessPermission::revertAssert();
}
```


