
# Tag Management Examples

## Basic Tag Placement
Let's assume you have been given a tag like the following:

```
<script async src='https://tag.simpli.fi/sifitag/945669a0-3942-4027-bff6-27e1cdca28cc'></script>
```

Please note that the long GUID after /sifitag/ will be different in your case.  Typically this tag should be placed at the bottom of every page in your website before the body tag, like so:

```
  <!-- other html content would go here and above -->

    <!-- Simpli.fi global tag: --->
    <script async src='https://tag.simpli.fi/sifitag/945669a0-3942-4027-bff6-27e1cdca28cc'></script>

  </body>
</html>
```

## Container Tags and Iframes

Simpli.fi does not recommend the use of "container tags" or placing tags inside a separate page which is called through an iframe. Doing this means that the simpli.fi tag will have no built-in way of knowing which page our tag is actually placed on, so the Tag Management rules in the Simpli.fi UI will not work.  The only URLs we will see are those of the iframe.  If you must use a container tag, a workaround would be to use the "referer" query string parameter in the tag, replacing the sample referer with the one that the iframe tag is placed on:


```
<script async src='https://tag.simpli.fi/sifitag/945669a0-3942-4027-bff6-27e1cdca28cc?referer=http://yourdomain.com/page-the-iframe-is-on'></script>

```

This will tell simpli.fi what page the container tag is placed on, so that you can create rules on how to handle the tage on those pages. 

Ideally, you would not hard code urls into the referer parameter, but instead use some sort of server-side programming logic to pass the URL of the page into the iframe and then to the container tag.  For example, if you were using Ruby on Rails on your website, and your container tag iframe page is at the URL "/container", you could dynamically pass the root URL of the outermost page, into the iframe, and then into the simpli.fi tracking tag:

On the outermost page with the "real" url or in a layout or template:

```
<!-- some other content would be here -->

<iframe src="/container?referer=<%= request.original_url%>" />

```

Then on the container page with the simpli.fi tag on it:

```
<script async src='https://tag.simpli.fi/sifitag/945669a0-3942-4027-bff6-27e1cdca28cc?referer=<%= params["referer"] %>'></script>
```

A similar mechanism could be used for any web programming language such as PHP, ASP.NET, JSP, etc.  

## Javascript Buttons and other Events

Sometimes, you will want to trigger a segment or conversion not if the user browses to a specific page or url, but if they perform some action on the page without leaving it using javascript.  This might open a dialog, pop open a new section, or completely change the content of the page without changing the url.  In these cases, one must combine the referer parameter with a bit of javascript to "trick" the Simpli.fi tag into thinking its on a new URL when it is not.

Let's say we want to put someone in a segment when the user views their shopping cart, but the shopping cart only opens a dialog window.  One could create a "fake" url representing the shopping cart event. 

```
<script language="javascript">
function viewCart() {
	//code to view the cart would go here.
}
</script>

<!--- Link or button to view the cart --->
<a href="#" onclick="viewCart();">View My Cart</a>
```

We would modify this code to drop the simpli.fi tag programmatically using javascript, with a fake URL representing that the user visited their shopping cart:

	
This is a variation of this stackoverflow.com article:
http://stackoverflow.com/questions/5235321/how-do-i-load-a-javascript-file-dynamically

```
<script language="javascript">
function viewCart() {
	//code to view the cart would go here.

	
   // DOM: Create the script element
   var jsElm = document.createElement("script");
   // set the type attribute
   jsElm.type = "application/javascript";
   // make the script element load the Simpli.fi tracking tag
   jsElm.src = "https://tag.simpli.fi/sifitag/945669a0-3942-4027-bff6-27e1cdca28cc?referer=http://example.com/view-shopping-cart";
   // finally insert the element to the body element in order to load the script
   document.body.appendChild(jsElm);
}
</script>

<!--- Link or button to view the cart --->
<a href="#" onclick="viewCart();">View My Cart</a>
```

Note that while the referer does have to have proper URL syntax it doesn't actually have to a be a real URL on your website.  Its simply there to label the trackign event has occurred.  We do recommend you use the real domain name of your website rather than a fake one, so none of the URL collide with another client's URLs.  






