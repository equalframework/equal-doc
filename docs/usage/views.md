To define the layout of the forms, the list of the fields and the possible interactions between them, we use a system of views similar to templates.

Each package has a folder named ‘views’ that contains, for each class, one or more HTML file.
These files are written in HTML5, and contain information about the fields and labels to display and their positioning, for the edition of the related class.

Again, this system is not the lightest in terms of overhead but has the advantage of being usable as is by the Javascript client (no server-side processing).

''**Filename format** is: //object_name//.(list | form).//view_name//.html''

A series of HTML5 tags has been chosen in order to be used in views based on the usage that is generally done of them:
  * reserved to inputs (as views are generated automatically) 
  * having no visual impact by default

==== Forms ==== 

===tag===
To define a form, we use the standard html FORM tag.

===attributes===
| form attribute     |usage           |
|-|-|
| action       | controller script (index.php?do=) to which form data will be relayed on submission |
| orientation  | 'portrait' or 'landscape' (for PDF output) |

| form attribute     |usage           |
|-|-|
| name       |  |
| visible  | js expression (returning true or false) |

| var attribute     | usage |
|-|-|
| id		| field name |
| widget	| force the widget to use (all types accepted + {'password', 'code', [more to come]} |
| view		| force view to use for the widget (if applicable) |
| format	| 'currency', 'percentage', 'boolean' |
| onchange	| js code triggered on item change |
| required	| true, false |



===Examples===


**js expressions**
```javascript
$.distinct
$.union 
$.intersect
$.except 

$.intersect(['accounts', 'staff'], user_groups()).length
$.intersect(['accounts'], user_groups()).length
$.inArray('accounts', user_groups()) >= 0
```

**buttons**
```html
	<button name="print as PDF" type="button" show="core_objects_view" view="form.report" output="pdf"></button>
	<button name="export as XLS" type="button" show="linnetts_invoice_export"></button>		
```

`packages/school/views/Student.form.default.html`

```html
<form action="core_objects_update">
    <section name="identification">
        <fieldset title="identification">
            <span width="50%">
                <label for="firstname"></label><var id="firstname" required="true"></var>
                <br />
                <label for="lastname"></label><var id="lastname" required="true"></var>
            </span>
            <span width="50%">
                <label for="birthdate"></label><var id="birthdate" required="true"></var>
                <br />
                <label for="subscription"></label><var id="subscription" required="false"></var>				
            </span>
        </fieldset>
    </section>
    <section name="data">
        <fieldset title="data">
            <span width="100%">			
                <label name="classes_ids"></label>
                <br />
                <var id="classes_ids" view="list.default"></var>
            </span>								
        </fieldset>
    </section>
</form>
```

`packages/core/views/User.form.login.html`
```html
<form>
<div width="100%">
	<fieldset title="identification">
		<span width="100%">
			<label for="login"></label><var id="login" required="true"></var>
			<br />
			<label for="password"></label>
			<var id="password" 
				 required="true" 
				 type="password" 
				 onchange="" 
				 onsubmit="$('#password', $form).val(lock(user_key(), $('#password', $form).val()))">
			</var>
		</span>
	</fieldset>
</div>
<div><button name="subscribe" type="button" onclick="window.location.href='?show=core_user_subscribe'"></button></div>
<div align="right"><button name="ok" type="button" action="core_user_login"></button></div>
</form>
```

==== Lists ==== 

===tag===
To define a list, we use the UL standard html tag.


===attributes===
| ul attribute     | usage |
|-|-|
| views		| json syntax of the views to use for editing and adding list items |
| domain	| json syntax of the domain array to apply on the list |
| sortname	| field name on which list must be sorted |
| sortorder	| sorting order ('desc' or 'asc') |


| li attribute     | usage |
|-|-|
| id		| field name |
| width		| % ('0%' makes the field invisible while still allowing to use it as search criteria) |
| align		| 'left', 'right' |
| search	| 0, 1, 2 (true, false accepted) |
| format	| 'currency', 'percentage', 'boolean' |

===Examples===

`packages/school/views/Student.list.default.html`

```html
<ul>
	<li id="id" width="10%"></li>
	<li id="firstname" width="23%"></li>
	<li id="lastname" width="23%"></li>	
	<li id="birthdate" width="22%"></li>	
	<li id="subscription" width="22%"></li>	
</ul>
```

`packages/knine/views/Article.list.default.html`

```html
<ul domain="[[['is_root', '=', 1]]]">
	<li id="id" width="10%"></li>	
	<li id="type" width="20%"></li>		
	<li id="title" width="70%"></li>			
</ul>
```

`packages/knine/views/Article.list.chapter.html`

```html
<ul views="{edit: 'form.chapter', add: 'list.chapter'}">
	<li id="id" width="10%"></li>	
	<li id="sequence" width="20%"></li>		
	<li id="title" width="70%"></li>			
</ul>
```

`packages/resilib/views/Category.list.default.html`

```html
<ul sortname="path">
	<li id="path" width="70%"></li>		
	<li id="mnemonic" width="30%"></li>
</ul>
```

==== Reports ==== 

===attributes===
| ul attribute     | usage |
|-|-|
| views		| json syntax of the views to use for editing and adding list items |
| domain	| json syntax of the domain array to apply on the list |
| sortname	| field name on which list must be sorted |
| sortorder	| sorting order ('desc' or 'asc') |
| group_by	| name of the field on which we want to group the result set |



| li attribute     | usage |
|-|-|
| id		| field name |
| width		| % ('0%' makes the field invisible while still allowing to use it as search criterea) |
| align		| 'left', 'right' |
| search	| 0, 1, 2 (true, false accepted) |
| format	| 'currency', 'percentage', 'boolean' |
| operation	| 'sum', 'diff', 'avg', 'min', 'max' |


```html
<ul group_by="customer_name">
	<li id="customer_name" width="40%"></li>
	<li id="total_repro" width="20%" operation="sum" align="right" search="false"></li>
	<li id="total_press_cost" width="20%" operation="sum" align="right" search="false"></li>		
	<li id="total_scr_cost" width="20%" operation="sum" align="right" search="false"></li>
	<li id="date" width="0%"></li>	
</ul>
```