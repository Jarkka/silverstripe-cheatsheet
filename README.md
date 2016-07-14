# SilverStripe Cheatsheet
Mission of this repo is simple. Syntax limited people like me can easily find basic code snippets for creating SilverStripe websites and projects without crawling old and already created code. I am a happy man if somebody else find this useful.

## SS Template snippets
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
<% require themedCSS('STYLE_FILE_NAME') %>
<title><% if ID == 1 %>$SiteConfig.Title<% else %>$SiteConfig.Title | $Title<% end_if %></title>
<script src="$ThemeDir/js/FILE_NAME.js"></script>
```

### Meta and favicon for \<head>
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

### Menu
```html
<% loop $Menu(1) %>
    <a href="$Link" class="navi-link $LinkingMode">$Title.XML</a>
    <% control Children %>
        <a href="$Link" class="secondary-navi-link $LinkingMode">$Title.XML</a>
    <% end_control %>
<% end_loop %>
```

## PHP Page snippets
### Page.php with header image and fetching image with custom width
```php
<?php
class Page extends SiteTree {
	private static $has_one = array(
		'HeaderImage' => 'Image'
	);
	
	public function OptimizedHeaderImage() {
		if ($this->HeaderImage()->exists()) {
		  return $this->HeaderImage()->SetWidth(CUSTOM_WIDTH);
		} else {
		  return null;
		}
	}

	public function getCMSFields() {
		$fields = parent::getCMSFields();
		
		$fields->addFieldToTab("Root.Main", new UploadField('HeaderImage', 'Header Image'), "Content");
		
		return $fields;
	}
}
```

### Custom dataobject with thumbnail image and Page as "holder"
```php
<?php
class CustomDataObject extends DataObject {
    static $singular_name = 'Custom';
    static $plural_name = 'Customs';
    
    static $db = array(
        'Name' => 'Varchar',
        'Description' => 'HTMLText',
        'SortOrder'=>'Int'
    );
    
    static $has_one = array(
        'Holder' => 'Page',
        'Image' => 'Image'
    );
    
    public function Thumbnail(){ 
        return $this->Image()->SetHeight(50); 
    }
    
    public function getCMSFields() {
        return new FieldList(
            new TextField('Name', 'Name'),
            new HTMLEditorField('Description', 'Description'),
            new UploadField('Image', 'Image')
        );
    }
    
    public static $default_sort='SortOrder';
}
```

### Custom Page with some CMS config and list of custom data objects
```php
<?php
class CustomPage extends Page {
    static $icon = "URL_TO_PAGE_ICON";
    static $singular_name = "PAGE_NAME";
    static $plural_name = "PAGE_NAME_PLURAL";
    static $description = 'PAGE_DESCRIPTION'; 
    
    static $has_many = array(
		  'CustomDataObjects' => 'CustomDataObject'
    );
    
    public function getCMSFields() {
        $fields = parent::getCMSFields();
        
        $config = GridFieldConfig_RecordEditor::create();
        
        // GridFieldExtensions needed for orderable rows
        $config->addComponent(new GridFieldOrderableRows('SortOrder'));
        
        $config->getComponentByType('GridFieldDataColumns')->setDisplayFields(array(
			'Thumbnail' => 'Image',
            'Name' => 'Name',
			'Description' => 'Description'
        ));
		
        $customDataObjectsField = new GridField(
            'CustomDataObject',
            'CustomDataObjects',
            $this->CustomDataObjects(),
            $config
        );
        
        $fields->addFieldToTab('Root.TAB_NAME', $customDataObjectsField); 
        
        return $fields;
    }
}
```
