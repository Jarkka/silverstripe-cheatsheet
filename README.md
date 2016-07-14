# SilverStripe Cheatsheet
Mission of this repo is simple. Syntax limited people like me can easily find basic code snippets for creating SilverStripe websites and projects without crawling old and already created code. I am a happy man if somebody else find this useful.

## Template snippets
### Theme structure
```
themes
- THEME_NAME
-- css
-- images
-- js
-- templates
--- Includes
---- file.ss
--- Layout
---- page.ss
--- page.ss
```

### Basic \<head>
```html
<% base_tag %>
<% require themedCSS('styles') %>
<title><% if ID == 1 %>$SiteConfig.Title<% else %>$SiteConfig.Title | $Title<% end_if %></title>
<script src="$ThemeDir/js/FILE_NAME.js"></script>
```

### Meta for \<head>
```html
<link rel="shortcut icon" href="$ThemeDir/images/favicon.ico" />

<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-title" content="TITLE_FOR_MOBILE_APP" />
<meta name="apple-mobile-web-app-status-bar-style" content="black" />

<meta content="website" property="og:type" />
<meta content="$AbsoluteLink" property="og:url" />
<meta content="NAME" property="og:title" />
<meta content="IMAGE_URL" property="og:image" />
<meta content="KEYWORDS">
<meta content="<% if $ID == 1 %>$SiteConfig.Tagline<% else %>$Content.FirstParagraph<% end_if %>" property="og:description" />
<meta content="<% if $ID == 1 %>$SiteConfig.Tagline<% else %>$Content.FirstParagraph<% end_if %>" name="Description" />
```

### \<body> with login form if no permission
```html
<% if Form %>
  <div id="login-form">
  	<a id="login-logo" href="/"></a>
  	<div class="full-div">
  		$Form
  	</div>
  </div>
<% else %>
  $Layout
<% end_if %>
```

### Include SS template from Includes
```html
<% include NAME %>
```

### Loop
```html
<% loop STUFF %>
  <div class="item">
    $Name
  </div>
<% end_loop %>
```
