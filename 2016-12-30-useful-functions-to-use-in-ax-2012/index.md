# Useful functions to use in AX 2012

Useful functions to use in AX 2012

```C#
public static container list2Con (List _list)
{
    container con;
    ListEnumerator listEnum;

    listEnum = _list.getEnumerator();

    while (listEnum.moveNext())
    {
        con += listEnum.current();
    }

    return con;
}

///Creates a file named _file with the content specified in _content parameter.
//See the class SysImportUtil class for file related functions like get temfolder,tempfilename..etc.
public static void createFile(str _file, str _content)
{
    #File
    TextIo file = null;
    ;
    new FileIOPermission(_file, #io_write).assert();

    // BP Deviation Documented
    file = new TextIo(_file, #io_write);
    file.write(_content);
    file.finalize();
}

///Creates an XML file named _file with the content specified in _content parameter. If the XML file
///can't be created then it will try to create a normal file by calling createFile method.
public static void createXmlFile(str _file, str _content)
{
    #File

    XmlDocument document = null;
    ;
    try
    {
        document = XmlDocument::newXml(_content, false);

        new FileIOPermission(_file, #io_write).assert();
        // BP Deviation Documented
        document.save(_file);
    }
    catch
    {
        SysImportUtil::createFile(_file, _content);
    }
}

///Reads an XML file specified by _file parameter.
public static str readXmlFile(str _file)
{
    #File

    XmlDocument file = null;
    ;
    try
    {
        new FileIOPermission(_file, #io_read).assert();
        // BP Deviation Documented
        file = XmlDocument::newFile(_file);
        return file.xml();
    }
    catch
    {
        return '';
    }
}

/// <summary>
/// Opens Bing maps with the address.
/// </summary>
/// <param name="_address">
/// The address to map.
/// </param>
public static void mapIt(LogisticsPostalAddress _address)
{
    #DEFINE.MapURL('http://maps.bing.com/default.aspx?where1=\%1')
    #DEFINE.comma(',')
    #DEFINE.newLine('\n')

    str     address;
    ;
    if (_address)
    {
        address = _address.Street + #comma +
                  _address.City + #comma +
                  _address.State + #comma +
                  _address.ZipCode + #comma +
                  _address.CountryRegionId;

        // Replace the newline with comma
        address = strReplace(address, #newline, #comma);
        // URL encode
        address = System.Web.HttpUtility::UrlEncode(address);
        // Add the address to the URL
        infolog.urlLookup(strFmt(#MapURL, address));
    }
}



/// <summary>
/// Function to get the list of Tables in AOT
/// </summary>
/// <returns>
/// In case the table is derived its parent i.e. base table is appended to it delimited by comma (,)
/// Output example
/// :Table1,Table1's Base Table : Table2 : Table3 : Table4 , Table4's Base Table
/// </returns>
static str getAOTTables()
{
    TreeNode            node;
    TreeNode            childNode;
    TreeNodeIterator    nodeIt;
    FilePath            path;
    TableId             tableId;
    Dictionary      dict = new Dictionary();
    str parentName;
    str fieldArr;

    path        = @'\Data dictionary\Tables';
    node        = TreeNode::findNode(path);
    nodeIt      = node.AOTiterator();
    childNode   = nodeIt.next();
    fieldArr ='';
    while(childNode)
    {
        tableId = tableName2id(childNode.treeNodeName());

        if ( !dict.tableObject(tableId).isTmp()  && !dict.tableObject(tableId).isMap() && !dict.tableObject(tableId).isView())
        {
            parentName = RetailUtils::getAOTTableParentName(tableId);
            if (parentName)
            {
                //Creating formated output , refer explaination and example on header
                fieldArr = fieldArr + ':' + strUpr(childNode.treeNodeName()) + "," + strUpr(parentName);
            }
            else
            {
                fieldArr = fieldArr + ':' + strUpr(childNode.treeNodeName());
            }
        }
        childNode = nodeIt.next();
    }
    return fieldArr;
}


/// <summary>
/// This method receives a table id and find the corresponding Base Table from AOT
/// This method takes care of Multilevel inheritence
/// </summary>
/// <param name="tableid">
/// tableid of derived table.
/// </param>
/// <returns>
/// Name of parent table.
/// </returns>
static str getAOTTableParentName(TableId tableid)
{
    Dictionary  dict = new Dictionary();
    TableId parentId, temp;
    str result;

    parentId = dict.tableObject(tableid).extends();

    // Loop until reached to the table for whcih Extends property is not defined
    while (parentId)
    {
        temp = dict.tableObject(parentId).extends();
        if (temp)
        {
            parentId = temp;
        }
        else
        {
            break;
        }
    }

    if (parentId)
    {
        result = tableId2name(parentId);
    }
    else
    {
        result = '';
    }
    return result;
}


/// <summary>
/// Function to accepts a table name and returns all its field
/// </summary>
/// <param name="tableName">
/// Table name for which field are to be retreived.
/// </param>
/// <returns>
/// Field are arranged in a : delimited string and passed back
/// Fields are read from AOT structure and defaults field are manually added
/// as those are not present in the tree structure
/// Example =  :Field1:Field2:Field3:Field4
/// </returns>
static str getAOTTableFields(str tableName)
{
    TreeNode            node;
    TreeNode            childNode;
    TreeNodeIterator    nodeIt;
    FilePath            path;
    TableId             tableId;
    str fieldArr;
    Dictionary      dict = new Dictionary();
    fieldArr = '';

    if (!tableName)
    {
        return fieldArr;
    }

    // Construct the path till table fields node
    path        = @'\Data dictionary\Tables\\' + tableName + '\\Fields' ;
    node        = TreeNode::findNode(path);
    nodeIt      = node.AOTiterator();
    childNode   = nodeIt.next();

    while(childNode)
    {
        fieldArr =  fieldArr + ':' + strUpr(childNode.treeNodeName());
        childNode = nodeIt.next();
    }

    tableId = tableName2id(tableName);
    if (dict.tableObject(tableId).dataPrCompany())
    {
        //Append the default Fields
        fieldArr =  fieldArr + ':' + 'DATAAREAID';
    }

    if (dict.tableObject(tableId).dataPerPartition())
    {
        fieldArr =  fieldArr + ':' + 'PARTITION';
    }

    return fieldArr;
}




// get enum values to container
static void Enum2container(Args _args)
{
   
    int         i = 0;
    DictEnum    dimCode;
    container   ret;
    ;

    dimCode = new DictEnum(enumnum(SysDimension));
    while(i < dimCode.values())
    {
        ret += dimCode.index2Name(i);
        i++;
    }
    info(strfmt("%1",con2Str(ret)));
}

public static boolean checkDateIsInPeriod(TransDate _transDate,
                                            PeriodCode  _periodCode = PeriodCode::Regular)
{
    return BudgetTransactionManager::validateTransactionDate(_transDate);
}


// New method for returning ledgerperiod last date
static transDate findLastOpenPeriod(transdate _ledgerTransDate)
{
    FiscalCalendarRecId     fiscalCalendarRecId;

    fiscalCalendarRecId = Ledger::fiscalCalendar(CompanyInfo::find().RecId);
    return FiscalCalendars::findLastDayofPeriod( fiscalCalendarRecId, _ledgerTransDate);
}

// New method for returning first open ledgerperiod first date
static transDate findFirstOpenPeriod(transdate _ledgerTransDate)
{
    FiscalCalendarRecId     fiscalCalendarRecId;

    fiscalCalendarRecId = Ledger::fiscalCalendar(CompanyInfo::find().RecId);
    return FiscalCalendars::findFirstDayofPeriod( fiscalCalendarRecId, _ledgerTransDate);

}


//Calculate Hash for the string.
public static str CalculateHash(str tb)
{

    str s;
    ClrObject obj;
    ClrObject md5;
    System.Text.StringBuilder sBuilder;
    ClrObject clrStr;
    ClrObject clrStrObject;
    System.Exception clrException;
    System.Array resultByteArray;
    int i;
    int arrayLength ;
    InteropPermission perm;

    perm = new InteropPermission(InteropKind::ClrInterop);
    perm.assert();
    try
    {
        obj = System.Text.Encoding::get_ASCII().GetBytes(tb);
        md5 = System.Security.Cryptography.MD5::Create();
        resultByteArray = md5.ComputeHash(obj);
        //BP deviation documented
        sBuilder = new System.Text.StringBuilder();
        arrayLength = resultByteArray.get_Length() ;
        // Loop through each byte of the hashed data
        // and format each one as a hexadecimal string.
        for (i = 0; i <arrayLength; i++)
        {
            clrStrObject = resultByteArray.GetValue(i);
            clrStr = clrStrObject.ToString('x2');
            sBuilder.Append(clrStr);
        }

        // Return the hexadecimal string.
        s = sBuilder.ToString();
    }
    catch (Exception::CLRError)
    {
        //BP deviation documented
        clrException = CLRInterop::getLastException();
        s = clrException.get_Message();
        error(s);
        throw error("@SYS106158");
    }

    CodeAccessPermission::revertAssert();
    return s;

}


//Transforms xml to another format of xml using xslt.
public static str transform(str aifXml, str xsltResourceName)
{
    #define.XsltArgumentList("System.Xml.Xsl.XsltArgumentList")
    #AOT

    ResourceNode resourceNode;
    TreeNode resourcesNode;
    container data;
    str xsltString;
    str doXml;

    System.Xml.Xsl.XslCompiledTransform transform;
    System.Text.StringBuilder           stringBuilder;
    System.IO.StringWriter              stringWriter;
    System.IO.StringReader              stringReader;
    System.Xml.XmlTextReader            xmlTextReader;
    System.IO.StringReader              stringReaderForXslt;
    System.Xml.XmlTextReader            xmlTextReaderForXslt;
    System.Xml.Xsl.XsltArgumentList     xsltArguments;
    System.IDisposable                  disposableObj;

    ;
    // Get the top Resource node in the AOT
    resourcesNode = infolog.findNode(#ResourcesPath);

    if (resourcesNode)
    {
        // Get the XML string holding the data from the resource item
        resourceNode = resourcesNode.AOTfindChild(xsltResourceName);
        if (resourceNode)
        {
            resourceNode.AOTload();
            data = SysResource::getResourceNodeData(resourceNode);
            xsltString = conpeek(data, 1);

        }
    }
    try
    {
        //Local XSLT
        transform = new System.Xml.Xsl.XslCompiledTransform();
        stringReaderForXslt = new System.IO.StringReader(xsltString);
        xmlTextReaderForXslt = new System.Xml.XmlTextReader(stringReaderForXslt);
        transform.Load(xmlTextReaderForXslt);

        //Transform the input AIF XML
        stringReader = new System.IO.StringReader(aifXml);
        xmlTextReader = new System.Xml.XmlTextReader(stringReader);
        stringBuilder = new System.Text.StringBuilder();
        stringWriter = new System.IO.StringWriter(stringBuilder);

        xsltArguments = ClrInterop::Null(#XsltArgumentList);

        transform.Transform(xmlTextReader, xsltArguments, stringWriter);

        doXml = stringBuilder.ToString();
    }
    catch (Exception::CLRError)
    {
        throw Global::error(ClrInterop::getLastException().toString());
    }

    if (!ClrInterop::isNull(xmlTextReaderForXslt))
    {
        disposableObj = xmlTextReaderForXslt;
        disposableObj.Dispose();
    }

    if (!ClrInterop::isNull(stringReaderForXslt))
    {
        disposableObj = stringReaderForXslt;
        disposableObj.Dispose();
    }

    if (!ClrInterop::isNull(xmlTextReader))
    {
        disposableObj = xmlTextReader;
        disposableObj.Dispose();
    }

    if (!ClrInterop::isNull(stringReader))
    {
        disposableObj = stringReader;
        disposableObj.Dispose();
    }

    if (!ClrInterop::isNull(stringWriter))
    {
        disposableObj = stringWriter;
        disposableObj.Dispose();
    }

    return doXml;
}




//Converts from string to Time.
static int str2Time(str 8 _timeStr)
{
    int     time;
    ;

    time += str2int(substr(_timeStr,0,2)) * 3600;
    time += str2int(substr(_timeStr,4,2)) * 60;
    time += str2int(substr(_timeStr,7,2));

    return time;
}

//This function is used to get xml string from xml file.
static public str getXMLstringFromFile(str _filepath)
{
    str xmlstring='';
    XmlDocument document=new XmlDocument();
    document.load(_filepath);
    xmlstring=document.innerXml();
    return xmlstring;
}

// this function is used to convert string to array.
public static Array str2array(str _inputString, str _delimiter = '","')
{
    List            list        = AmcBankFunctions::strSplit(_inputString, _delimiter);
    ListEnumerator  enumerator  = list.getEnumerator();

    Array           a           = new Array(Types::String);
    int             i           = 1;
    str             tmpString;
    ;

    if (strLen(_inputString) == 0 || strScan(_inputString, _delimiter, 1, strLen(_inputString)) == 0)
        return a; // this is not a XTL record
    enumerator.reset();
    while (enumerator.moveNext())
    {
        tmpString = enumerator.current();

        // Removes first substring's preceding " and last substring's succeeding "
        if (strLen(tmpString) > 0)
        {
            if (i == 1 && tmpString)
                tmpString = subStr(tmpString, 2, strLen(tmpString));
            if (i == list.elements())
                tmpString = subStr(tmpString, 1, strLen(tmpString)-1);
        }

        a.value(i, tmpString);
        i++;
    }

    return a;
}


/// <summary>
///     Prefixes a string with a parameter provided character
/// </summary>
/// <param name="inputString">
///     The integer that is to be converted into a string
/// </param>
/// <param name="outputStringLength">
///     The length of the final string being returned
/// </param>
/// <param name="prefixWithChar">
///     The character which to prefix the output string with
/// </param>
/// <returns>
///     The converted integer as a string with the parameter provided length (possible prefixed with provided

char)
/// </returns>
public static str prefixString(str inputString, int outputStringLength, str 1 prefixWithChar)
{
    str outputString = inputString;

    if (prefixWithChar == '')
        return outputString;

    while (strLen(outputString) < outputStringLength)
    {
        outputString = prefixWithChar + outputString;
    }

    return outputString;
}


// to test that we have access right for the the file or folder.
public static client IO_Status writeFileAndClose(Filename filename)
{
    TextIo textIo;
    ;

    new FileIOPermission(filename,'rw').assert();

    textIo = new TextIo(filename,'w');

    if (textIo!=null)
        textIo.write('This is a test file for checking the access rights');

    CodeAccessPermission::revertAssert();

    if (textIo!=null)
        return textIo.status();

    return IO_Status::WriteError;
}


// send email using smtp
//Please change the parameters accordingly for your client
public static boolean emailSmtpSend(str         _receiver,
                                    str         _subject,
                                    str         _body,
                                    Filename    _attachment = '')
{
    AmcBankParameters                       amcBankParameters;

    System.Net.Mail.Attachment              attachment;
    System.Net.Mail.AttachmentCollection    attachmentCollection;
    System.Net.Mail.MailAddress             mailFrom;
    System.Net.Mail.MailAddress             mailTo;
    System.Net.Mail.MailMessage             mailMessage;
    System.Net.Mail.SmtpClient              smtpClient;
    ;

    if (!_receiver)
        return checkFailed("@ABA32081");

    amcBankParameters = AmcBankParameters::find();

    try
    {
        mailFrom    = new System.Net.Mail.MailAddress(amcBankParameters.SmtpSenderAddress);
        mailTo      = new System.Net.Mail.MailAddress(_receiver);
        mailMessage = new System.Net.Mail.MailMessage(mailFrom, mailTo);
        mailmessage.set_Subject(_subject);
        mailmessage.set_Body(_body);

        // Adding attachment
        if (_attachment)
        {
            attachment = new System.Net.Mail.Attachment(_attachment);
            attachmentCollection = mailMessage.get_Attachments();
            attachmentCollection.Add(attachment);
        }

        smtpClient = new System.Net.Mail.SmtpClient(amcBankParameters.SmtpServerName,

amcBankParameters.SmtpPortNumber);
        smtpClient.Send(mailmessage);

        info(strFmt("@SYS80635", _receiver));

        attachmentCollection.Dispose(); // Releases attachement resources (files)

    }
    catch (Exception::CLRError)
    {
        // If a CLR exception occurs it is important that the exceptions is shown (which it is not per default)
        error(CLRInterop::getLastException());
        attachmentCollection.Dispose(); // Releases attachement resources (files)

        return checkFailed(strFmt("@ABA32082",  _receiver));
    }
    catch
    {
        attachmentCollection.Dispose(); // Releases attachement resources (files)

        return checkFailed(strFmt("@ABA32082",  _receiver));
    }

    return true;
}


// archive the files.
public static void fileArchive(Filename _file, FilePath _archivePath)
{
    Filename            filenameArchive;

    InteropPermission   interopPermission = new InteropPermission(InteropKind::ClrInterop);
    Set                 interopPermissionSet = new Set(Types::Class);
    ;
    if (!_archivePath)
        return;

    // Granting file permission rights
    interopPermissionSet.add(interopPermission);
    CodeAccessPermission::assertMultiple(interopPermissionSet);

    filenameArchive = _archivePath + AmcBankFile::getFileNameAndExtension(_file);

    System.IO.File::Delete(filenameArchive);
    System.IO.File::Move(_file, filenameArchive);

    // Reverting file permission rights
    CodeAccessPermission::revertAssert();
}

// getting the filename and extesion in container.
public static Filename getFileNameAndExtension(Filename _filename)
{
    ;
    if (!_filename)
        return '';

    return strFmt('%1%2', conPeek(Global::fileNameSplit(_filename), 2),     // Filename
                          conPeek(Global::fileNameSplit(_filename), 3));    // File extension (including

prefixed dot (.))
}

//FolderExists
public static boolean folderExists(FilePath _directory)
{
    boolean             dirExists;

    InteropPermission   interopPermission = new InteropPermission(InteropKind::ClrInterop);
    Set                 interopPermissionSet = new Set(Types::Class);
    ;

    // Granting file permission rights
    interopPermissionSet.add(interopPermission);
    CodeAccessPermission::assertMultiple(interopPermissionSet);

    dirExists = System.IO.Directory::Exists(_directory);

    // Reverting file permission rights
    CodeAccessPermission::revertAssert();

    return dirExists;
}


/// <summary>
///  internal use only.
/// </summary>
/// <param name="text">
/// A String value.
/// </param>
/// <returns>
/// A String value.
/// </returns>
private str trimLeadingTabs(str text)
{
    int len = strlen(text);
    int start = 1;

    while ((start <= len) && (substr(text, start, 1) == '\t'))
        start++;

    text = substr(text, start, len - start + 1);

    return text;
}


/// <summary>
///  internal use only.
/// </summary>
/// <returns>
/// A String value.
/// </returns>
public static str getClrErrorMessage()
{
    #File
    str exceptionMessage;
    System.Exception exObject, innerException;
    ;

    new InteropPermission(InteropKind::ClrInterop).assert();

    // BP deviation documented
    exObject = CLRInterop::getLastException();
    if(exObject)
    {
        // We will ignore the first message since it is always a fixed message of
        // "Exception has been thrown from target of invocation", this is from the reflection API being used
        // "ClrObject could not be created." - This is also of no use in most cases
        innerException = exObject.get_InnerException();
        while(innerException)
        {
            // BP deviation documented
            exceptionMessage = exceptionMessage + #delimiterSpace + CLRInterop::getAnyTypeForObject

(innerException.get_Message());
            innerException = innerException.get_InnerException();
        }
    }

    CodeAccessPermission::revertAssert();

    return exceptionMessage;

}

//Parse date from string
public static date parseDate(str dateText)
{
    str dateTimeText;
    ClrObject dateTimeObject;
    utcdatetime dateTimeValue;
    date dateValue;


    dateTimeText = strFmt('%1T00:00:00Z', dateText);

    // BP Deviation Documented
    dateTimeObject = Microsoft.Dynamics.IntegrationFramework.Util::ParseXmlDateTime(dateTimeText, false);

    // If the returned object is null, then deserialization failed
    if (CLRInterop::isNull(dateTimeObject))
    {
        throw error(strfmt("@SYS112380", dateText, enum2str(Types::Date)));
    }

    dateTimeValue = CLRInterop::getAnyTypeForObject(dateTimeObject);

    // If the utcdatetime value is null, then the value is outside the valid range
    // unless the input was a null datetime
    if (dateTimeValue == utcDateTimeNull() && !AifUtil::isNullDateTime(dateTimeObject))
    {
        throw error(strfmt("@SYS112380", dateText, enum2str(Types::Date)));
    }

    dateValue = DateTimeUtil::date(dateTimeValue);

    return dateValue;
}

//Parse DateTime from string
public static utcdatetime parseDateTime(str dateTimeText, System.DateTimeKind expectedDateTimeKind)
{
    ClrObject dateTimeObject;
    utcdatetime dateTimeValue;
    System.DateTime systemDateTimeValue;

    // BP Deviation Documented
    dateTimeObject = Microsoft.Dynamics.IntegrationFramework.Util::ParseXmlDateTime(dateTimeText, false);

    // If the returned object is null, then deserialization failed
    if (CLRInterop::isNull(dateTimeObject))
    {
        throw error(strfmt("@SYS112380", dateTimeText, enum2str(Types::UtcDateTime)));
    }

    dateTimeValue = CLRInterop::getAnyTypeForObject(dateTimeObject);

    // If the utcdatetime value is null, then the value is outside the valid range
    // unless the input was a null datetime
    if (dateTimeValue == utcDateTimeNull() && !AifUtil::isNullDateTime(dateTimeObject))
    {
        throw error(strfmt("@SYS112380", dateTimeText, enum2str(Types::UtcDateTime)));
    }

    // If the value is of unexpected kind, throw an error.
    systemDateTimeValue = dateTimeObject;
    if (systemDateTimeValue.get_Kind() != expectedDateTimeKind)
    {
        throw error(strfmt("@SYS112380", dateTimeText, enum2str(Types::UtcDateTime)));
    }

    return dateTimeValue;
}

// get the sequence number for the dateformat.
public static int getSequenceForDateFormat( DateFormat _dateFormat)
{
    ;

    switch (_dateFormat)
    {
        case DateFormat::YMD:
            return 321;
        case DateFormat::DMY:
            return 123;
        case DateFormat::DYM:
            return 132;
        case DateFormat::MDY:
            return 213;
        case DateFormat::MYD:
            return 231;
        case DateFormat::YDM:
            return 312;
        default:
            return 123;
    }
}

// returns time from str.
static int str2Time(str 8 _timeStr)
{
    int     time;
    ;

    time += str2int(substr(_timeStr,0,2)) * 3600;
    time += str2int(substr(_timeStr,4,2)) * 60;
    time += str2int(substr(_timeStr,7,2));

    return time;
}

// <CostCenter:Department> _dimaatributesString,
// <1010:MBS> dimValueString
public static RecId generateDefaultDimension(Str                        _dimValueString,
                                             str                        _dimaatributesString,
                                             str                        _delimeter)
{
    container                           dimAttributeList,
                                        dimValueList;
    int                                 j;

    RecId                               defDimRecId;
    DimensionAttributeValueSetStorage   storage;

    if (_dimaatributesString && _dimValueString)
    {
        dimAttributeList = str2con(_sourceProperties, _delimeter,false);
        dimValueList     = str2con(_dimValueString, _delimeter,false);

        if(!dimValueList)
        {
            throw error(strFmt("This dimvalue string was not available"));
        }

        storage = new DimensionAttributeValueSetStorage();

        for (j=1;j<=conLen(dimValueList);j++)
        {
            if(conPeek(dimValueList,j))
            {
                DMFDimensionHelper::dynamicDimensionCreation(dimAttributeList, dimValueList, j);

                storage.addItem(DimensionAttributeValue::findByDimensionAttributeAndValue(DimensionAttribute::findByName(conPeek(dimAttributeList,j)), conPeek(dimValueList,j), false, true));
            }
        }

        defDimRecId = storage.save();
    }
    return defDimRecId;

}


//Generate the Dimension String from default dimension
// <CostCenter:Department> dimAttributeString
// <1010:MBS> dimValueString
public static str generateDefaultDimensionStr(RefRecId  _defaultDimension,str _dimattrString,
                                                  str _delimeter)
{
    DMFDefaultDimensionStr              defaultDimensionStr;
    DimensionAttributeValueSetItem      setItem;
    DimensionAttributeValue             dimAttrValue;
    DimensionAttribute                  dimAttr;
    container                           con;
    Int                                 i;
    DimensionValue                      displayValue;

    con = str2con(_dimattrString,_delimeter,false);

    if (_dimattrString && _defaultDimension)
    {
        for( i = 1; i <= conLen(con); i ++)
        {
            displayValue = conPeek(con,i);

            select RecId, DisplayValue from setItem where setItem.DimensionAttributeValueSet == _defaultDimension
                join RecId from dimAttrValue where
                     dimAttrValue.RecId == setItem.DimensionAttributeValue &&
                     dimAttrValue.IsDeleted == false
                 join RecId from dimAttr
                    where  dimAttr.RecId == dimAttrValue.DimensionAttribute
                       &&  dimAttr.Name  == displayValue;

                if (defaultDimensionStr)
                {
                    defaultDimensionStr += _delimeter;
                }

                defaultDimensionStr += setItem.DisplayValue;
        }
    }

    return defaultDimensionStr;
}


static int strLineCount(str freeTxt)
{
    int  start  = 1;
    int  end;
    int  length;
    int  lines  = 0;

    length=strLen(freeTxt);

    end = strScan(freeTxt, '\n', start, length);

    while (start)
    {
        lines+=1;
        if (end)
        {
            start=end+1;
            end = strScan(freeTxt, '\n', start, length);
        }
        else
            start=0;
    }

    if (!lines)
        lines=1;

    return lines;
}


/// <summary>
/// Determines whether the specified string is a date time string.
/// </summary>
/// <param name="_value">
/// The string to check.
/// </param>
/// <returns>
/// true if it is date time; otherwise, false.
/// </returns>
/// <remarks>
/// Input can have a format like "5/7/2010 12:00:00 AM" or "2010-05-07T00:00:00”. Both cases will be
/// handled.
/// </remarks>
public static boolean isDateTimeStr(str _value)
{
    boolean isDateTime;
    System.Text.RegularExpressions.Regex regex;
    System.Text.RegularExpressions.Match regexMatch;
    #define.MatchTimeRegex(@"[\sT]\d{1,2}:\d{1,2}:\d{1,2}")
    #define.ISODateTimeIdentifier("T")
    #define.ZeroTime("00:00:00")
    #define.DefaultLCID(1033)

    // check if in ISO format.
    isDateTime = strFind(_value, #ISODateTimeIdentifier, 0, strLen(_value)) > 0;
    if(!isDateTime)
    {

        // search for a time on the value
        // BP deviation documented
        regex = new System.Text.RegularExpressions.Regex(#MatchTimeRegex);

        // BP deviation documented
        regexMatch = regex.Match(_value);
        isDateTime = regexMatch.get_Success();
    }

    return isDateTime;
}


//Swapping the key value from map
/// <summary>
/// Creates a <c>Map</c> class with the value and key swapped.
/// </summary>
/// <param name="_key2ValueMap">
/// Map that has the keys and values swapped.
/// </param>
/// <returns>
/// A map that has the value switched with the keys.
/// </returns>
public static Map getValue2KeyMap(Map _key2ValueMap)
{
    // reverses the valid values map. The original map contains <key, label>, we will create a <label, key>
    Map value2KeyMap;
    MapEnumerator mapEnum;

    if(_key2ValueMap)
    {
        value2KeyMap = new Map(_key2ValueMap.valueType(), _key2ValueMap.keyType());
        mapEnum = _key2ValueMap.getEnumerator();
        while(mapEnum.moveNext())
        {
            value2KeyMap.insert(mapEnum.currentValue(), mapEnum.currentKey());
        }
    }

    return value2KeyMap;
}
```

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
