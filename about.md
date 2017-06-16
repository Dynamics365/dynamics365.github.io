---
comments: true
layout: page
title: About
share: true
title: Contact Me - Freelancer
tags: [Freelancer, Dynamics AX, Dynamics 365, development, consultant]
---

### Hi There!!

My name is Max, I'm Developer, Technical Consultant and I'm Vietnamese.
Here is some of my experience in AX expertise

#### Things are in my mind when I do the customizations

  * Minimizing the footprint of changes (I know how painful is when merging code from different partners or upgrade or installing hotfix).
  * Where to make the change (always at the highest level and in this order: class, table, form data source feld/method, form control).
  * Using field groups (The reason this reduces footprint).
  * Upgrades and system maintainability.
  * Design for a service-oriented architecture.
  * Design for code reuse.
  * Apply Dynamics AX design patterns.
  * Apply Best pratice.
	
#### Most Dynamics technologies which I've been working with	

  * **PowerBI**
  * **SSRS and Financial reporting**
  * **Application Integration Framework (AIF)**
  * **Extensible Data Security**
  * **TFS and DAX Source Version Control**
  * **Batch**
  * **File integration using WINAPI**
  
#### Industries Which I've been working with

  * Manufacturing
  * Professional Services
  * Fleet Management
  * Retail
  
#### Language

  * **X++**
  * **SQL**
  * **C#**
  * **XML**
  
#### ERP and CRM

  * **Dynamics AX Version**
	* Dynamics AX 2012 R2 CU7 and above
    * Dynamics AX 2012 R3 CU8 and above
    * Dynamics AX 365 for operation (aka AX7)
  * **Dynamics CRM Version**
    * CRM 2011
  * **Microsoft Dynamics 365**
    * Microsoft Dynamics 365 for Sales
    * Microsoft Dynamics 365 for Customer Service
	
#### Most functions which I've been working with

  * **Financials**
	* Financials Dimension
	* Chart of accounts
	* Financial statements – Balance sheet, Income statement
  * **Trade and Logicstics**
	* Item creation
	* Sales process
	* Purchase process
	* Transfer order
	* Supply chain management
  * **Production**
	* BOM
	* Routes and operations
	* Purchase process
	* Transfer order
  * **Retails**
	* Installation and configrution Retails POS
	* The Async Service
	* Real-time Service
	* Certificate Management
  * **Warehouse management**
	* Workflows
	* Picking and packing
	* Mobile devices
	
:)
	
## Contact

You can send me an email at: _**luan52@outlook.com**_ or use the form bellow:

<form action="http://formspree.io/luan52@outlook.com" method="post">
	<label for="name">Name</label>    
	<input type="text" id="name" name="name" class="full-width"><br>
	<label for="email">Email Address</label>
	<input type="email" id="email" name="_replyto" class="full-width"><br>
	<label for="message">Message</label>
	<textarea name="message" id="message" cols="30" rows="10" class="full-width"></textarea><br>
	<div markdown="0"><input type="submit" value="Send" class="btn btn-success" /></div>
</form>



<link rel='stylesheet' type='text/css' href='{{site.url}}/assets/css/contact.css' />


<script>
	jQuery(document).ready(function($){
	if( $('.floating-labels').length > 0 ) floatLabels();

	function floatLabels() {
		var inputFields = $('.floating-labels .cd-label').next();
		inputFields.each(function(){
			var singleInput = $(this);
			//check if user is filling one of the form fields
			checkVal(singleInput);
			singleInput.on('change keyup', function(){
				checkVal(singleInput);
			});
		});
	}

	function checkVal(inputField) {
		( inputField.val() == '' ) ? inputField.prev('.cd-label').removeClass('float') : inputField.prev('.cd-label').addClass('float');
	}
	});
</script>
