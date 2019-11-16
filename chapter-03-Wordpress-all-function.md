GitHub Markdown syntax: https://help.github.com/articles/basic-writing-and-formatting-syntax/

# এই ক্লাশে যে সব ওয়ার্ডপ্রেস ফাংশন শিখানো হয়েছে
- add_theme_support( 'custom-header' );
- current_theme_supports( 'custom-header')
- the_custom_logo()
- comments_open()
- comments_template()
- get_template_part()
- next_post_link()
- previous_post_link()
- register_sidebar(
- is_active_sidebar( 'sidebar-name' )
- dynamic_sidebar( 'sidebar-name' )
- post_password_required()
- get_the_password_form()
- register_nav_menu( 'topmenu', __('Top Menu', 'alpha') )
- wp_nav_menu()
- wp_enqueue_script( 'featherlight', get_template_directory_uri().'/assets/js/featherlight.min.js', array('jquery'), '1.7.13', true )
- is_front_page()


# Class 3.12 সিংগেল পোস্ট ভিউতে কমেন্ট ফর্ম দেখানো
কোন পোস্টের কমেন্ট এনাবল/ডিজাবল স্টেট ভ্যালু চেক করার পর, কমেন্ট টেমপ্লেট লোড করা আবশ্যক।
```php
if(comments_open()): ?>
<div class="col-md-10 offset-md-1">
<?php
comments_template();
?>
</div>
<?php endif; ?>
```

# Class 3.15 get_template_part, include এবং require এর মাঝে পার্থক্য
get_template_part()
1. এই ফাংশন সবসময়ই থিমের রুট ফোল্ডার থেকে টেমপ্লেট পার্ট খুঁজে থাকে।
2. থিমের টেমপ্লেট পার্টগুলোকে সবসময়ই এই ফাংশন দিয়ে লোড করতে হবে।
3. কিন্তু, ক্লাস ফাইল বা অন্যান্য ফাংশন ফাইলকে include_once বা require_once দিয়ে লোড করতে হবে।

# Class 3.16 সিঙ্গেল পোস্ট ভিউতে পোস্ট নেভিগেশন 
সিঙ্গেল পোস্ট ভিউতে পোস্ট নেভিগেশন
```php
next_post_link();
previous_post_link();
```

# Class 3.17 উইজেট এরিয়া রেজিস্টার করা এবং সিঙ্গেল পোস্ট ভিউ তে সাইডবার দেখানো 
উইজেট এরিয়া রেজিস্টার করা এবং সিঙ্গেল পোস্ট ভিউ তে সাইডবার দেখানো
```php
register_sidebar(
		array(
			'name'					=> __( "Single Post Sidebar", 'alpha' ),
			'id'					=> 'sidebar-1',
			'description'			=> __( 'Right Sidebar', 'alpha' ),
			'before_widget'			=> '<section id="%1$s" class="widget %2$s">',
			'after_widget'			=> '</section>',
			'before_title'			=> '<h2 class="widget-title">',
			'after_title'			=> '</h2>'
		)
	);
add_action('widgets_init', 'alpha_sidebar');
```

সাইডবার দেখানো আগে চেক করে নিতে হবে, সাইডবারে কোন কন্টেন্ট আছে কি না:
```php
<?php
if(is_active_sidebar( 'sidebar-1' ))
{
    dynamic_sidebar( 'sidebar-1' );
}
?>
```

# Class 3.19 পাসওয়ার্ড প্রটেকটেড পোস্ট ম্যানেজ করা

* Method 1: Write Code in index.php or where necessary
```php
if( !post_password_required() )
{
    the_excerpt();
}
else
{
    echo get_the_password_form();
}
```
অথবা
```php
if( post_password_required() )
{
    echo "This post is protected!";
}
else
{
    the_excerpt();
}
```

* Method 2: Apply filter for the_excerpt in functions.php file
```php
function alpha_the_excerpt( $excerpt )
{
	if( !post_password_required() )
	{
		return $excerpt;
	}
	else
	{
		echo get_the_password_form();
	}
}
add_filter( "the_excerpt", "alpha_the_excerpt");
```

* To **omit** the word **"Protected: "** from protected article's title, write another filter in functions.php file.
```php
function alpha_protected_title_change()
{
	return "%s";
}
add_filter( 'protected_title_format', "alpha_protected_title_change" );
```

# WPTD Class 3.20

* মেনু তৈরীর ধাপ গুলো হল:
1. functions.php তে মেনু লোকেশন তৈরী করা (register_nav_menu)
2.  মেনু ডিসপ্লে করা (wp_nav_menu)
3. Bootstrap 4 এ লিস্ট আইটেম কে horizontal alignment করতে হলে filter দিয়ে nav_menu_css_class হুক কে কলব্যাক ফাংশন দিয়ে প্রয়োজনীয় css যুক্ত করতে হয়।

* মেনু লোকেশন তৈরী করা
```php
register_nav_menu( 'topmenu', __('Top Menu', 'alpha') );
```

* মেনু ডিসপ্লে করা
```php
<?php
wp_nav_menu(
    array(
        'theme_location' => 'topmenu',
        'menu_id' => 'topmenucontainer',
        'menu_class' => 'list-inline text-center'
    )
);
?>
```

* ফিল্টার দিয়ে প্রয়োজনীয় css যিুক্ত করা
```php
// Add a class to menu list item
function alpha_menu_item_class( $classes, $item )
{
	$classes[] = 'list-inline-item';
	return $classes;
}
add_filter( 'nav_menu_css_class', 'alpha_menu_item_class', 10, 2 );
```


# Class 3.21 থিমে এক্সটার্নাল জাভাস্ক্রিপ্ট ফাইল এনকিউ করা এবং লাইটবক্স/পপআপ দেখানো
* **External** JS File enque from functions.php file
```php
wp_enqueue_script( 'featherlight', get_template_directory_uri() . '/assets/js/featherlight.min.js', array('jquery'), '1.7.13', true );
```

# Class 3.22 ইনটার্নাল জাভাস্ক্রিপ্ট ফাইল এনকিউ করা এবং ডিপেন্ডেন্সি নিয়ে আলোচনা

* **Internal** JS File enque from functions.php file
```php
wp_enqueue_script( 'alpha-main', get_template_directory_uri() . '/assets/js/main.js', null, '0.0.1', true );
// OR, the file below can be used, but, get_theme_file_uri() function isn't available before 4.7 version of WordPress
wp_enqueue_script( 'alpha-main', get_theme_file_uri('/assets/js/main.js'), null, '0.0.1', true );
```

* Trick
We can popup the image in big size with jQuery.
Put the following code in main.js file in assets/js/main.js file.
```jquery
// This script taken all image's src and put the image URL in the href attr
$('.popup').each(function(){
    var image = $(this).find("img").attr("src");
    $(this).attr("href", image);
});
```

# Class 3.24 সিঙ্গেল পেজ ভিউ এবং পেজ টেমপ্লেটের সাথে পরিচয় (খুবই ইম্পর্ট্যান্ট)

* Single page view and Page template

কোন পেজের জন্য আলাদা টেমপ্লেট ব্যবহার করতে হলে থিমের রুটে একটা নতুন PHP ফাইল বানাতে হবে। মনে করি, About পেজের জন্য নতুন টেমপ্লেট বানালাম about-page-template.php ফাইল।

এই ফাইলের ভিতরে প্রথমে লিখতে হবে:
```php
<?php
/*
 * Template Name: About Page Template
 */
?>
```

এরপর **page.php** ফাইলের কনটেন্ট কপি করে নিয়ে এসে পেস্ট করব। এবার এই ফাইলের মধ্যে প্রয়োজনীয় পরিবর্তন গুলো করে ফেলতে হবে।

# ৩.২৭ - কাস্টোমাইজারের সাহায্যে হেডার ইমেজ পরিবর্তন (add_theme_support)

Custom Header সাপোর্ট বাই-ডিফল্ট দেয়া থাকে না। এটাকে add_theme_support হুক দিয়ে এ্যাক্টিভিট করে নিতে হবে। functions.php ফাইলে after_setup_theme হুকের কলব্যাকে custom-header এর জন্য নিচের কোডটি লিখব:

```php
// Add Custom Header support in the customizer
add_theme_support( 'custom-header' );
```

এবার wp_header হুকের কলব্যাকে নিচের মত করে কোড লিখব:

```php
<?php
if( is_front_page() )
{
	if( current_theme_supports( 'custom-header') )
	{ ?>
<style>
.header {
	background-image: url(<?php echo header_image(); ?>);
	background-color: #333;
	background-position: center;
	background-size: cover;
}
</style>
	<?php
	}
}
?>
```

এখানে, প্রথমে চেক করা front page এ (অথবা, page এ) আছি কিনা। তারপর .header নামের একটি ক্লাসে ব্যাকগ্রাউন্ড ইমেজ হিসাবে কাস্টম হেডারের ইমেজটা দেখানো হচ্ছে।

# ৩.২৮ - কাস্টোমাইজারে কাস্টম হেডার টেক্সট কালার পরিবর্তন

যখন থিমে custom-header সাপোর্ট নেয়া হয়, তখন একটি এ্যারে আর্গুমেন্ট পাস করা যায়।

```php
// Video 3.28
$alpha_custom_header_details = array(
	'header-text' => true,
	'default-text-color' => '#222',
);

// Video 3.27
// Theme-wide Custom Header
add_theme_support( 'custom-header', $alpha_custom_header_details );
```

এতে কাস্টমাইজারে দুইটা নতুন সেটিং অপশন যুক্ত হয়। একটি Colors, অপরটি Site Identity। এর মধ্যে “Display Site Title and Tagline" নামে চেকবক্স।

আর্গুমেন্টে header-text কে false করলে, এই চেকবক্সটা চলে যায়।

এরপর wp_head হুকের কলব্যাক ফাংকশনে প্রয়োজনীয় চেকআপ ও সিএসএস যুক্ত করে নিতে হয়।

# ৩.২৯ - থিম সাপোর্টে কাস্টম লোগোর ব্যবহার

থিমে custom-logo এর সাপোর্ট যুক্ত করতে হলে after_setup_theme হুকের কলব্যাক ফাংকশনে নীচের কোড লিখতে হবে:

```php
// Custom Logo Support in the Theme
$alpha_custom_logo_defaults = array(
	"width" => "100",
	"height" => "100",
);
add_theme_support( 'custom-logo', $alpha_custom_logo_defaults );
```

এরপর, যেখানে কাস্টম লোগে দেখাবে (আমার ট্রেনিং থিমে hero.php ফাইলে), সেখানে এসে নীচের কোডগুলো লিখতে হবে। কাস্টম লোগে দেখানোর আগে **current_theme_support** ফাংকশন দিয়ে চেক করে নিচ্ছি যে, থিমে আদৌ custom-logo এর জন্য কোন সাপোর্ট যুক্ত করা আছে কি না।

```php
<?php if( current_theme_supports( "custom-logo" ) ): ?>
<div class="header-logo text-center">
    <?php the_custom_logo(); ?>
</div>
<?php endif; ?>
```
## ৩.৩০ - কাস্টম হেডার ইমেজে ক্রপিং নিয়ে বিস্তারিত

custom-header হুকের সাথে যে আর্গুমেন্ট পাস করা হয়, তার ৪টা প্যারামিটারে হেডার ইমেজের heigt, width, flex-height এবং flex-width প্যারামিটারগুলো পাস করা যায়। flex-height এবং flex-width দিয়ে বুঝানো হচ্ছে যে,ইমেজের নির্ধারিত উচ্চতা ও প্রস্থ ছাড়াও ক্রপ করার সময় ইচ্ছামত উচ্চতা ও প্রস্থ সেট করা যায়।

```php
$alpha_custom_header_details = array(
	'header-text' => true,
	'default-text-color' => '#222',
	'width' => 1200,
	'height' => 600,
	'flex-height' => true,
	'flex-width' => true
);
```

## ৩.৩১ - কাস্টম ব্যাকগ্রাউন্ড

custom-background সাপোর্ট নিলে ২টা সুবিধা যুক্ত হয়।

1. কাস্টমাইজার সেকশনে “Background Image" নামে নতুন ফিচার যুক্ত হবে।
2. Colors এর মধ্যে "Background Color" সেট করার অপশন যুক্ত হবে।

## ৩.৩২ - থিমে ৪০৪ এরর পেজ তৈরী করা

প্রথমে, থিমের রুটে 404.php নামে একটি ফাইল তৈরী করতে হবে। এরপর একে ইচ্ছামত স্টাইল করে নিলেই হবে।
