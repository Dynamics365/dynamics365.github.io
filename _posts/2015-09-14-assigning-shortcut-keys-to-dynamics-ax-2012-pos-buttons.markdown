---
title: The Type system of Dynamics AX 2012
layout: post
date: 2016-11-24 08:22:39.000000000 +07:00
author: Max Nguyen
categories: [ax2012, trick]
tags: [trick, programming]
---

<p>Although the POS screens are designed to be touched, you may also want to give the power POS users the ability to navigate through the screens with shortcuts and hot keys on the keyboard.</p>
<p>In this example we will show how you can easily configure shortcut keys through the POS designer.<span id="more-1101"></span></p>
<h2>How to do it…</h2>
<p>To assign a shortcut key to a POS button, follow these steps:</p>
<p>There are two ways that you can update the POS buttons within Dynamics AX. The first is through the Screen layouts, and also through the Button grids. In this example we will use the first, which you can access from the Retail area page, by clicking on the Screen layouts menu item within the POS folder of the Setup group.</p>
<p>&nbsp;</p>
<div style="padding-left:30px;">When the Screen layouts dialog box is displayed, select the layout that you want to change and click on the Designer button in the menu bar.</div>
<div style="padding-left:30px;">
<p style="padding-left:30px;"><img title="Assigning Shortcut Keys to Dynamics AX 2012 POS Buttons" src="{{ site.baseurl }}/assets/052913_1432_assigningsh1.png" alt=" Assigning Shortcut Keys to Dynamics AX 2012 POS Buttons" width="489" height="240" /></p>
<p style="padding-left:30px;">Assigning Shortcut Keys to Dynamics AX 2012 POS Buttons</p>
</div>
<p><!--more--></p>
<ul>
<li>When the Screen layouts dialog box is displayed, select the layout that you want to change and click on the Designer button in the menu bar.</li>
<li>
<div>This will take you into the interactive design mode for the POS screen. Right-mouse-click on the button that you want to change and select the Button propertiesoption – in this case we will change the Voids &amp; returns.</div>
<p><img src="{{ site.baseurl }}/assets/052913_1432_assigningsh2.png" alt=" " /></li>
<li>
<div>This will open up the button properties. Now you can change the text for the button, and put an “&amp;” in front of for the key that you want to assign the shortcut to.</div>
<p><img src="{{ site.baseurl }}/assets/052913_1432_assigningsh3.png" alt=" " /></li>
<li>In the designer, we can continue drilling through the buttons to assign shortcuts to the submenu items as well.</li>
<li>In this case we will also assign the shortcut key of “V” to the Void transaction button.</li>
<li>
<div>Once you have finished updating the POS screen layouts and buttons, click on theDistribution schedule menu item in the Data Distribution folder of the Periodicgroup of the Retail Area page.</div>
<p><img src="{{ site.baseurl }}/assets/052913_1432_assigningsh4.png" alt=" " /></li>
<li>Select the N-1090 job and click on the Run directly button in the menu bar to distribute all of the design changes to all of the registers.</li>
</ul>
<h2>How it Works…</h2>
<p>Now, when we are in the POS Register, we can quickly void a transaction by pressing ALT-V, ALT-V.</p>
<p><img src="{{ site.baseurl }}/assets/052913_1432_assigningsh5.png" alt=" " /></p>
