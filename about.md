---
comments: true
layout: page
title: About
share: true
title: Contact Me - Freelancer
tags: [Freelancer, Dynamics AX, Dynamics 365, development, consultant]
---

### Hi There!!

<<<<<<< HEAD
My name is Max, I'm Vietnamese.

=======
My name Max, I'm Vietnamese.
>>>>>>> 1b8ba44940c7ea8729a5666e8d1acbc6f069ad9c

## Freelancer

Some of my freelance experience

**ERP and CRM**

  * **Dynamics AX Version**
    * Dynamics AX 2012 R2 CU7 and above
    * Dynamics AX 2012 R3 CU8 and above
    * Dynamics AX 365 for operation (aka AX7)
  * **Dynamics CRM Version**
    * CRM 2011
  * **Microsoft Dynamics 365**
    * Microsoft Dynamics 365 for Sales
    * Microsoft Dynamics 365 for Customer Service
	
**Language**

  * **X++**
  * **SQL**
  * **C#**
  * **XML**
  
**Module which I've been working with**

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
