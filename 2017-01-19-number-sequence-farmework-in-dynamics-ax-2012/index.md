# Number sequence farmework overview in Dynamics AX 2012


<figure class='center '>
  <a href="/imagesposts/number-sequence-overview.png"><img src="/imagesposts/number-sequence-overview.png" alt=""></a>
</figure>

For this demo, I will create New module along with NumberSequence, about creating Number Sequence *without* module you also use same steps just leave some steps base on Design picture above.

## ETD

Create ETD `ContosoId` extends `num` datatype.

## Table

Create `Contoso` Table with `ContosoId` field.

## Enum

Create a new enum value `Contoso` in BaseEnum `NumberSeqModule`.

This value will be used to link number sequence to the module and to restrict displayed number sequence by module in Form.

<!--more-->

## NumberSeqModule Class

Create `NumberSeqModuleXXX` class

Create a new `NumberSeqModuleXXX` class, such as `NumberSeqModuleContoso`, which extends the `NumberSeqApplicationModule` class. The sample code for creating this class is as follows:

```C#
public class NumberSeqModuleContoso extends NumberSeqApplicationModule
{
}

protected void loadModule()
{
	NumberSeqDatatype datatype = NumberSeqDatatype::construct();
	;

	/* Contoso Id */
	datatype.parmDatatypeId(extendedtypenum(ContosoId));
	datatype.parmReferenceHelp("ContosoId");
	datatype.parmWizardIsContinuous(true);
	datatype.parmWizardIsManual(NoYes::No);
	datatype.parmWizardIsChangeDownAllowed(NoYes::No);
	datatype.parmWizardIsChangeUpAllowed(NoYes::No);
	datatype.parmWizardHighest(999999);
	datatype.addParameterType(NumberSeqParameterType::DataArea, true, false);
	this.create(datatype);
}

public NumberSeqModule numberSeqModule()
{
	;
	return NumberSeqModule::Contoso;
}
```

Use of the `DataArea` segment in *Step 4* to describe the default segment for the extended data types used for `ContosoId`.

Note In **Microsoft Dynamics AX 2009**, number sequence references could be initialized by restarting the Application Object Server (AOS). In **Microsoft Dynamics AX 2012**, the initialization of references to populate the `NumberSequenceDatatype` and `NumberSequenceParameterType` tables has moved to the initialization checklist. To initialize the newly created references, run a job that executes the `LoadModule` method likes below.
	
```C#
static void loadNumSeq (Args _args)
{
	//define the class variable
	NumberSeqModuleContoso seqMod = new NumberSeqModuleContoso ();
	//load the number sequences that were not generated
	seqMod.load();
}
```

You can also reinitialize all references by running a job that executes the `LoadAll` method in the `NumberSequenceModuleSetup` class. However, for reinitializing all references, you must ensure that there are no existing number sequences already defined in the system.

Then run the wizard in *Organization Administration > CommonForms > Numbersequences > Numbersequences > Generate > run the wizard*.

## Parameters Table and Form

Create a Number sequences page in the parameters form of the new module.

You need to Create `ContosoParameters` Table along with form, See existing forms such as `CustParameters` or `LedgerParameters` for examples of the implementation.

_These forms are using **DetailsFormMaster** form parten as Best Practice for Setup form._

### Create `ContosoParameters` Table
		
* Add field key, Extends from `ParametersKey`

* Properties on Field key Visible = false, AllowEdit = false, AllowEditOnCreate = false
	
* Create index name Key with AllowDuplicate = No.
	
* Set table properties.
	
* TableContent = Default data

* ConfigurationKey

* CacheLookup = Found

* TableGroup = Parameter

* PrimaryKey = Key

* ClusterKey = Key
	
The sample code for creating method this table as below:
		
```C#
void delete()
{
	throw error("@SYS23721");
}
public void initValue()
{
	;
	super();
	// Key is set to mandatory so set it to 1
	this.Key = 1;
}
static ContosoParameters find(boolean _forupdate = false)
{
	ContosoParameters parameter;

	if (_forupdate)
	{
		parameter.selectForUpdate(_forupdate);
	}

	select firstonly RecId from parameter
		where parameter.Key == 1;

	if (!parameter && !parameter.isTmp())
	{
		Company::createParameter(parameter);
	}

	return parameter;
}
client server static NumberSeqModule numberSeqModule()
{
	;
	return NumberSeqModule::Contoso;
}
public server static NumberSequenceReference numRefContosoId()
{
	;
	NumberSeqScopeFactory::CreateDataAreaScope(curext());
	return NumberSeqReference::findReference(extendedtypenum (ContosoId));

}
```

### Create `ContosoParameters` form
		
Note This form can only be used for references that have a scope of DataArea. The administration forms described in the Setup and Administration of number sequences section can be used for references that have any scope. These forms can be found in *Organization Administration > Common > Number Sequences*

The data source of Parameters form likes picture below, you can also refer to `CustParameters` form for design.

<figure class='center '>
  <a href="/imagesposts/ContosoParameters.png"><img src="/imagesposts/ContosoParameters.png" alt=""></a>
</figure>

Code example for form methods:
		
```C#
public class FormRun extends ObjectRun
{
	NumberSeqReference          numberSeqReference;
	boolean                     runExecuteDirect;
	TmpIdRef                    tmpIdRef;
	NumberSeqScope              scope;
	NumberSeqApplicationModule  numberSeqApplicationModule;
	container                   numberSequenceModules;
}

void init()
{
	;
	this.numberSeqPreInit();
	super();
	ContosoParameters::find();
	this.numberSeqPostInit();
}

void numberSeqPostInit()
{
	numberSequenceReference_ds.object(fieldNum(NumberSequenceReference, AllowSameAs)).visible(numberSeqApplicationModule.sameAsActive());
	referenceSameAsLabel.visible(numberSeqApplicationModule.sameAsActive());
}

void numberSeqPreInit()
{
	;
	runExecuteDirect            = false;

	numberSequenceModules       = [NumberSeqModule::Contoso];
	numberSeqApplicationModule  = new NumberSeqModuleContoso ();
	scope                       = NumberSeqScopeFactory::createDataAreaScope();
	NumberSeqApplicationModule::createReferencesMulti(numberSequenceModules, scope);

	tmpIdRef.setTmpData(NumberSequenceReference::configurationKeyTableMulti(numberSequenceModules));
}
```
	
Code example for `NumberSequenceReference` data source methods
	
```C#
void removeFilter()
{
	;
	runExecuteDirect = false;
	numbersequenceReference_ds.executeQuery();
}

void executeQuery()
{
	if (runExecuteDirect)
	{
		super();
	}
	else
	{
		runExecuteDirect = true;
		this.queryRun(NumberSeqReference::buildQueryRunMulti(numberSequenceReference,
															 tmpIdRef,
															 numberSequenceTable,
															 numberSequenceModules,
															 scope));
		numbersequenceReference_ds.research();
	}
}
```
		
## How to use on Table

Set number sequence in Contoso Table

```C#
private void setContosoId()
{
	NumberSeq num;
	NumberSequenceReference numberSequenceReference;
	;
	numberSequenceReference = CVRParameters::numRefContosoId();

	if (numberSequenceReference)
	{
		num = NumberSeq::newGetNum(numberSequenceReference);
		this.Id = num.num();
	}
}
```

Optional method – in case you don’t want to expose Number sequence on Form Level

```C#
public void initValue()
{
	;
	super();

	// Initialise the title id
	this. setContosoId ();
}

public void insert()
{
	;
	if(!this.Id)
	{
		this. setContosoId ();
	}

	super();
}
```

From now on you can create new record in Contoso Table with number sequence.

## How to use on Form

How to use on form level (In case you don’t want to expose NS in Table Level)

* In the class declaration of the form that will be accessing data, add a variable declaration for the number sequence handler. The following example shows the variable definition for a number sequence handler.

```C#
public class FormRun extends ObjectRun
{
	NumberSeqFormHandler numberSeqFormHandlerContosoId;
}
```

* Add the `NumberSeqFormHandler` method to the form. The code in this method will create an instance of the number sequence form handler and return it.

```C#
public NumberSeqFormHandler numSeqFormHandlerContosoId()
{
	if (!numberSeqFormHandlerContosoId)
	{
		numberSeqFormHandlerContosoId = NumberSeqFormHandler::newForm(ContosoParameters:: numRefContosoId().NumberSequenceId,
															 element,
															 Contoso_ds,
															 fieldNum(Contoso, ContosoId)
															);
	}
	return numberSeqFormHandlerContosoId;
}
```

* Add **create, delete, and write** methods to the data source of the table that contains the field for which the number sequence is being used. The following code examples show these methods that are added to the data source for the Contoso table to support the number sequence for the `ContosoId` field.

```C#
public void create(boolean _append = false)
{
	//before create, (ensure the number seuence has not run out of numbers)
	element.numSeqFormHandlerContosoId().formMethodDataSourceCreatePre();

	// start: inherited table code
	// we need to let the create happen so the user can
	// choose the type
	super(_append);

	//number sequence, create action, (get next number)
	element.numSeqFormHandlerContosoId().formMethodDataSourceCreate();
}
public void delete()
{
	//release the number sequence value.
	element. numSeqFormHandlerContosoId().formMethodDataSourceDelete();

	//delete the record
	super();
}
public void write()
{
	super();
	element. numSeqFormHandlerContosoId().formMethodDataSourceWrite();
}

public boolean validateWrite()
{
	boolean         ret;
	ret = super();
	ret = element.numberSeqFormHandler().formMethodDataSourceValidateWrite(ret) && ret;
	if (ret)
	{
		Contoso.validateWrite();
	}
	return ret;
}
Link Active()
public void linkActive()
{
	;
	element.numberSeqFormHandler().formMethodDataSourceLinkActive();
	super();
}
```
		
## Optional method

Continuous sequence

To avoid having gaps for continuous sequence you should add this code to the delete of the table.

```C#
public void delete()
{
	super();
	NumberSeq::releaseNumber(ContosoParameters::numRefContosoId().NumberSequenceId, this.ContosoId);
}
```
		
## Testing

Testing Number sequence by Job

```C#
static void Max_numberSeqRefCustAccount(Args _args)
{
	NumberSequenceReference numberSeqRefCustAccount = CustParameters::numRefCustAccount();
	NumberSeq               numSeqCustAccount;
	try
	{
//Use the TTS level to decide whether sequence is consumed or not.
		ttsBegin;
		if (numberSeqRefCustAccount)
		{
			numSeqCustAccount = NumberSeq::newGetNum(numberSeqRefCustAccount);
			if (numSeqCustAccount)
			{
				info(numSeqCustAccount.num());
			}
		}
		ttsCommit;
	}
	catch (Exception::Error)
	{
	   info("Caught 'Exception::Error'.");
	}
}
```

Thank you for reading!
