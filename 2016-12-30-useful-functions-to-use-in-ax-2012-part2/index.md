# Useful functions to use in AX 2012 part 2


```c#
//Show viewhistory on form datasources if you have ValidaTimeState Enabled DateTime enabled on the tables of that form. 
//create this method in Global class so you can call whereever you want in form button click as below.

void clicked()

{
    buttonHistoryClick(element, this);
    Super();
}

static public void buttonHistoryClick(FormRun _formRun, FormButtonControl _fbc)
{
    void changeDataSources(ValidTimeStateAutoQuery _from, ValidTimeStateAutoQuery _to, boolean _allowDelete)
    {
        Counter         dataSourceNo;
        FormDataSource  formDataSource;

        for (dataSourceNo=1;_formRun.dataSourceCount()>=dataSourceNo;dataSourceNo++)
        {
            formDataSource = _formRun.dataSource(dataSourceNo) as FormDataSource;
            if (formDataSource.validTimeStateAutoQuery() == _from && new DictTable(formDataSource.table()).isValidTimeStateTable())
            {
                formDataSource.validTimeStateAutoQuery(_to);
                switch (_to)
                {
                    case ValidTimeStateAutoQuery::AsOfDate:
                        formDataSource.validTimeStateAutoQuery(ValidTimeStateAutoQuery::AsOfDate);
                        formDataSource.query().resetValidTimeStateQueryType();
                        formDataSource.allowDelete(_allowDelete);
                        break;
                    case ValidTimeStateAutoQuery::DateRange:
                        formDataSource.validTimeStateAutoQuery(ValidTimeStateAutoQuery::DateRange);
                        formDataSource.query().validTimeStateDateTimeRange(DateTimeUtil::minValue(), DateTimeUtil::maxValue());
                        formDataSource.allowDelete(_allowDelete);
                        break;
                }
                formDataSource.executeQuery();
            }
        }
    }

    if (_fbc.labelText() == "@SYS110266")
    {
        changeDataSources(ValidTimeStateAutoQuery::AsOfDate, ValidTimeStateAutoQuery::DateRange, false);
        _fbc.text("Stoping History");
        _fbc.normalImage("10006");
    }
    else
    {
        changeDataSources(ValidTimeStateAutoQuery::DateRange, ValidTimeStateAutoQuery::AsOfDate, true);
        _fbc.Text("@SYS110266
        _fbc.normalImage("10007");
    }

}
// this method is used to create filenameTimeStamp.

static FileName createFilenameTimeStamp()
{
    FileName    ret;
    Microsoft.Dynamics.IntegrationFramework.Adapter.FileSystem       fileSystem;
    #Aif

    fileSystem      = AifUtil::getClrObject(#FileSystemProgId);

    ret = fileSystem.GetCurrentTimestamp();

    return ret;
}


// This method is used to split the string, and returns the conatiner with the position defined in the parameter
public static str rsaStrSplit(str _splitString,str _splitchar,int _pos)
{
    List strlist=new List(Types::String);
    ListIterator    iterator;
    container       packedList;
    ;
    strlist=strSplit(_splitString,_splitchar);
    iterator = new ListIterator(strlist);
    while(iterator.more())
    {
        packedList += iterator.value();
        iterator.next();
    }
    return conPeek(packedList,_pos);

}

public static str encrypt(str _input, str _salt = '')
{
    System.Security.Cryptography.SHA512Managed  sha512managed = new System.Security.Cryptography.SHA512Managed();
    System.Text.Encoding                        encoding = System.Text.Encoding::get_UTF8();

    System.Byte[]                               inputBytes;
    System.Byte[]                               resultBytes;

    int                                         i;
    str                                         returnString;
    ;

    new InteropPermission(InteropKind::ClrInterop).assert();

    inputBytes = encoding.GetBytes(strLwr(_salt) + _input); // Convert lower case salt + input into byte array

    // The input is hashed 1024 times for attack resiliency
    for (i = 0; i < 1024; i++)
    {
        resultBytes = resultBytes ? resultBytes : inputBytes; // First loop uses input for hashing
        resultBytes = sha512managed.ComputeHash(resultBytes);
    }

    returnString = System.Convert::ToBase64String(resultBytes);

    CodeAccessPermission::revertAssert();

    return returnString;
}

private static Map fileGetList(FilePath            _filePathArchive)

{
  
    Map                 mapFiles;

    InteropPermission   interopPermission = new InteropPermission(InteropKind::ClrInterop);
    Set                 interopPermissionSet = new Set(Types::Class);
    System.Array        arrayFiles;

    int                 i;
    ;

    // Granting file permission rights
    interopPermissionSet.add(interopPermission);
    CodeAccessPermission::assertMultiple(interopPermissionSet);

    mapFiles = new Map(Types::String, Types::String); // Key = return file | Value = archive path

        if (!System.IO.Directory::Exists(_filePathArchive))
        {
             throw  error("Path doesnt exist");
        }

        arrayFiles = System.IO.Directory::GetFiles(_filePathArchive);

        // CLRInterop::getAnyTypeForObject method is used to handle difference in AX and System types (e.g. System.Int32 != int)

        for (i = 0; i < CLRInterop::getAnyTypeForObject(arrayFiles.get_Length()); i++)
        {
       mapFiles.insert(CLRInterop::getAnyTypeForObject(arrayFiles.GetValue(i)), _filePathArchive);
        }
    }

    // Reverting file permission rights
    CodeAccessPermission::revertAssert();

    return mapFiles;
}


/// <summary>
///  Gets the SenderID from AIF xml .
/// </summary>
/// <param name="messagePartsXml">
/// An <c>AifXml</c> value.
/// </param>
/// <returns>
/// An instance of the <c>str document Namespace</c> class.
/// </returns>
public static str getSenderIDValue(AifXml messagePartsXml)
{
     XmlTextReader               xmlReader;
    str value,currentElement,pureElement;

;
#Aif
    xmlReader = XmlTextReader::newXml(messagePartsXml);
    while (xmlReader.Read())
    {
        switch (xmlReader.nodeType())
        {
             case XmlNodeType::Element:
                  currentElement = xmlReader.name();
                 break;
             case XmlNodeType::Text:
                  pureElement = subStr(currentElement,strFind(currentElement,':',1,256)+1,256);
                    switch (pureElement)
                    {
                       case "SenderId":
                        {
                            value=xmlReader.value();
                            return value;
                        }
                        break;
                    }
              break;
        }
    }

    return value;
}
```

