A module for the Play! framework.
Enables you to have dynamic pages created and displayed.

Add the dependency in dependency.yml
```
  require
       - play -> cms 1.0
```
Add routes:
```
GET   /page	module:cms
```
Only connected users can edit pages, and (currently) they must have the "admin" profile, so check against that profile in your controllers.Security.java, e.g.:
```
	static boolean check(String profile) {
		String username = connected();
		if (username == null)
			return false;
		User user = User.findByName(username);
		if (user == null)
			return false;
		if ("admin".equals(profile))
			return user.admin;
		return false;
	}
```
Create one (or more) template to display the pages, say std.html:
```
#{extends 'main.html' /}
#{set title:page.title /}

<div class="normal">
${page.body.raw()} 
</div>

#{cms.editPage tmpl:'std',pageName:page.name /}
```
(This assumes your have a general template main.html to inherit.) Watch the $page variable holding the page attributes name (the id and URL part), title and body. Use body.raw() because you will have HTML content in there. The cms.editPage Tag renders an edit.button, but only if an admin is logged in.

Optionally create a template `cms/cms.html`, which will be used to render the cms admin pages.

Now navigate to
```
/page/admin
```
and create and edit pages using the [Tiny MCE](http://www.tinymce.com) HTML editor.
After your have created a page of name page1, eventually use the following URL to display it in your template std.html:
/page/std/page1.

Congratulations! You just added a CMS to your Play! App!

You could also use this CMS to edit parts of your app. Simply put parts of your template into a cms display tag:
```
    	<div id="footer">
#{cms.display 'footer' }
    		<a href="/pages/terms.html">Terms</a>
#{/cms.display}
    	</div>
```
After having rendered the changed template once, there exists a CMS page 'footer'. Since this pages body is empty, you will not notice any differences in your app - yet! But if you start editing the new page and insert some content, this will be displayed instead of the content from your template.

Simple remove the page or clear its body to re-establish the old content.