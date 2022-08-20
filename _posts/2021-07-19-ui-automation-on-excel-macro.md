---
layout: post
title: "UI Automation On Excel Macro"
date: 2021-07-19 10:04:01+0800
categories:
tags:
  - VBA
navi-enable-vba: true
navi-name: 'UI Automation On Excel Macro'
navi-order: ''
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: How to use the Microsoft UI Automation component. Microsoft UI Automation is a new accessibility framework for Microsoft Windows, available on all operating systems that support Window Presentation Foundation(WPF)
excerpt: How to use the Microsoft UI Automation component. Microsoft UI Automation is a new accessibility framework for Microsoft Windows, available on all operating systems that support Window Presentation Foundation(WPF)
---
<!--navigation bar-->
<!-- <div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-vba %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div> -->
<!--navigation bar-->

In this chapter, I am gonna illustrate how to use the Microsoft UI Automation component. Microsoft UI Automation is a new accessibility framework for Microsoft Windows, available on all operating systems that support Window Presentation Foundation(WPF).

# Basic Concepts
It's easy to build up robustness and assistive programs with Microsoft UI Automation. Several concepts need to be familiar before this article. Microsoft UI Automation contains four main components, as shown in the following table.

|Component   |DLL   |Description   |
|---|---|---|
|Provider API  |UIAutomationProvider.dll and UIAutomationTypes.dll   |A set of interface definitions that are implemented by UI Automation providers, objects that provide information about UI elements and respond to programmatic input.   |
|Client API   |UIAutomationClient.dll and UIAutomationTypes.dll   |A set of types for managed code that enables UI Automation client applications to obtain information about the UI and to send input to controls.   |
|Core   |UiAutomationCore.dll   |The underlying code (sometimes called the UI Automation core) that handles communication between providers and clients.   |
|ClientsideProviders   |UIAutomationClientsideProviders.dll   |A set of UI Automation providers for standard legacy controls. (WPF controls have native support for UI Automation.) This support is automatically available to client applications.   |

The following dialogue represented the relationship between **Automation Provider**, **Automation Client**, and **Automation Core**.

<div class="imgcenter" markdown="1">
![Alt][7]*Relationship Between Provider, Client And Core*
</div>

Basically, for developers, there are two perspectives: to create support for custom controls (using the **Provider API**), and creating applications that use the UI Automation core to communicate with UI elements (using the **Client API**). For better understanding about Microsoft UI automation element tree structure, need to make an acquaintance with **Control Patterns** and **Control Type**.

[AutomationElement][8] objects expose common properties of the UI elements they represent. One of these properties is the control type, which defines its basic appearance and functionality as a single recognizable entity: for example, a button or check box.

In addition, elements expose control patterns that provide properties specific to their control types. Control patterns also expose methods that enable clients to get further information about the element and to provide input.

UI Automation Client also provides a mechanism that enables a client to gather information through **events**. You can register specific event notifications, and the element's properties and control patterns will pass into their handlers when the event raise.


Next, I am going to lead you to create a simple macro excel application with the Microsoft UI Automation Client component step by step. I have wrote a snippet of Microsoft UI Automation's usage at [UI Automation Snippet Example][6], which may be assistive for you.

# Adding Reference

Create an Excel file and then open its VBA editor by striking `[ALT]`+`[F11]`. 

At the menu `Tools` -> `References...`, scroll down to find and refer `UIAutomationClient` component. 

<div class="imgcenter" markdown="1">
![Alt][5]*Adding UI AutomationClient reference*
</div>

# Creating CUIAutomation Object
<pre>
 <vb-keywords>Dim</vb-keywords> oAutomation <vb-keywords>As</vb-keywords> <vb-keywords>New</vb-keywords> CUIAutomation
</pre>

# Searching Elements
UI Automation Client provides several available ways to find elements. You can use `inspecter.exe` as an assistive software, which can inspect details of UI elements(such as: **classname**, **automation id**, and **name**). 

`Scope` and `Condition` are required for searching elements. `Scope` specifies the scope of variant operations in UI Automation tree. `Condition` used in filtering when searching for elements in the UI Automation tree. Take a look at [TreeScope enumeration][1] and [IUIAutomationCondition interface][2] for more details.

**Find first child or descendant element**

<pre>
<vb-commets>' Creating CUIAutomation element</vb-commets>
<vb-keywords>Dim</vb-keywords> oAutomation <vb-keywords>As New</vb-keywords> CUIAutomation

<vb-commets>' find first child element with classname</vb-commets>
<vb-commets>' oAutomation.GetRootElement is the top-level element</vb-commets>
<vb-keywords>Dim</vb-keywords> MyElement1 <vb-keywords>As</vb-keywords> UIAutomationClient.IUIAutomationElement
<vb-keywords>Set</vb-keywords> MyElement1 = oAutomation.GetRootElement.FindFirst(TreeScope_Children, UiAutomation.CreatePropertyCondition(UIAutomationClient.UIA_ClassNamePropertyId, "class name"))

<vb-commets>' find first descendant element with automation id</vb-commets>
<vb-keywords>Dim</vb-keywords> MyElement2 <vb-keywords>As</vb-keywords> UIAutomationClient.IUIAutomationElement
<vb-keywords>Set</vb-keywords> MyElement2 = MyElement1.FindFirst(TreeScope_Descendants, UiAutomation.CreatePropertyCondition(UIAutomationClient.UIA_AutomationIdPropertyId, "automation id"))

<vb-commets>' find first parent element with name</vb-commets>
<vb-keywords>Dim</vb-keywords> MyElement3 <vb-keywords>As</vb-keywords> UIAutomationClient.IUIAutomationElement
<vb-keywords>Set</vb-keywords> MyElement3 = MyElement2.FindFirst(TreeScope_Parent, UiAutomation.CreatePropertyCondition(UIAutomationClient.UIA_NamePropertyId, "name"))
</pre>

**Find All elements**

<pre>
<vb-commets>' Creating CUIAutomation element</vb-commets>
<vb-keywords>Dim</vb-keywords> oAutomation <vb-keywords>As</vb-keywords> <vb-keywords>New</vb-keywords> CUIAutomation

' find all elements with "button" as their's classname
<vb-keywords>Dim</vb-keywords> MyElement4 <vb-keywords>As</vb-keywords> UIAutomationClient.IUIAutomationElementArray
<vb-keywords>Set</vb-keywords> MyElement4 = MyElement3.FindAll(TreeScope_Children, UiAutomation.CreatePropertyCondition(UIAutomationClient.UIA_ClassNamePropertyId, "button"))
</pre>


**Get Element's unique identifier**

<pre>
<vb-commets>' Get a unique identifier of an element</vb-commets>
<vb-keywords>Dim</vb-keywords> uniqueIdentifier <vb-keywords>As</vb-keywords> Variant
 uniqueIdentifier = MyElement3.GetRuntimeId()
</pre>

The identifier is only guaranteed to be unique to the UI of the desktop on which it was generated. Identifiers can be reused over time.

<blockquote class="quote">
<b>NOTE:</b>  
Identifier may change in the future !!!
</blockquote>

**Focus Element**

<pre>
<vb-commets>' make MyElement3 be focused on the screen window</vb-commets>
MyElement3.SetFocus
</pre>

# Retrieving Control Pattern

Assumpting that you had known the difference between Control Type and Control Pattern, If not, take a look at the previous article. Control Pattern provides the  client a way to manipulate ui elements(such as: click, input, or dropdown, etc...)

Each UI Elements have at least one pattern, Take a look at [Control Pattern Identifiers][3] for more details.

**Input**

<pre>
<vb-commets>' input text</vb-commets>
<vb-keywords>Set</vb-keywords> oPattern = MyElement3.GetCurrentPattern(UIAutomationClient.UIA_LegacyIAccessiblePatternId)
oPattern.SetValue ("input text")
</pre>

**Click**
<pre>
<vb-commets>' click a button</vb-commets>
<vb-commets>' btnElement is a button element</vb-commets>
<vb-keywords>Set</vb-keywords> oPattern = btnElement.GetCurrentPattern(UIAutomationClient.UIA_InvokePatternId)
ePattern.Invoke
</pre>

# Conclusion
Above straightly illustrated the steps of creating an excel macro application with Microsoft UI Component.  The following reviews might help you to understand better.
* Adding `UIAutomationClient` in reference window
* Searching elements through `FindFirst` or `FindAll`
* Performing actions through `Control Pattern`


[1]: https://docs.microsoft.com/en-us/windows/win32/api/uiautomationclient/ne-uiautomationclient-treescope
[2]: https://docs.microsoft.com/en-us/windows/win32/api/uiautomationclient/nn-uiautomationclient-iuiautomationcondition
[3]: https://docs.microsoft.com/en-us/windows/win32/winauto/uiauto-controlpattern-ids
[4]: ../../../../vba/2021/07/18/ui-automation-on-vba/
[5]: /public/img/2021-07-19-ui-automation-on-excel-macro.png
[6]: https://gist.github.com/voltwu/fe90b3e7dd001c11228597f0ac29ab19
[7]: /public/img/2021-07-18-ui-automation-on-vba-a.png
[8]: https://docs.microsoft.com/en-us/dotnet/api/system.windows.automation.automationelement?view=net-5.0