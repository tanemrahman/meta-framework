# Class 4.2 কাস্টম ফিল্ডের সাথে পরিচয়

## এই পর্বে সে সকল ফাংশনের সাথে পরিচয় হয়েছে

- get_post_meta( get_the_ID(), "placeholder", true );
- wp_localize_script( '**main-jquery**', 'datedata', array( "year" => $launcher_year, "month" => $launcher_month,  "day" => $launcher_day ) );
- echo get_page_template();
- echo basename( get_page_template() );


```
get_post_meta( int $post_id, string $key = '', bool $single = false )
```
Retrieve post meta field for a post.

Return: (mixed) Will be an array if $single is false. Will be value of meta data field if $single is true.

```php
get_post_meta( get_the_ID(), "placeholder", true );
```

# Class 4.3
## ওয়ার্ডপ্রেসে পিএইচপি স্ক্রিপ্ট থেকে জাভাস্ক্রিপ্টের কাছে ডেটা পাঠানো 

functions.php ফাইলে wp_enqueue_scripts হুকের কলব্যাক ফাংশনে নীচের কোড দিয়ে জাভাস্ক্রিপ্ট এর কাছে ভ্যালু পাস করা যায়।

```php
wp_enqueue_script( '**main-jquery**', get_theme_file_uri("/assets/js/main.js"), array('jquery'), null, true );

$launcher_year = get_post_meta( get_the_ID(), 'year', true );
$launcher_month = get_post_meta( get_the_ID(), 'month', true );
$launcher_day = get_post_meta( get_the_ID(), 'day', true );

 // wp_localize_script( $handle, $name, $data );

wp_localize_script( '**main-jquery**', 'datedata',
  array(
    "year" => $launcher_year,
    "month" => $launcher_month,
    "day" => $launcher_day
  )
);
```
**গুরুত্বপূর্ণ নোট**

যে main.js ফাইলকে যে **handle** দিয়ে নামকরণ করা হয়েছে, সেই একই **handle** এর নাম wp_localize_script এ ব্যবহার করতে হবে। তা নইলে ডাটা পাস হবে না।

এই ডাটাটা পাস করা হয়েছে main.js ফাইলে। জেএস ফাইলটি datedata নামে একটি অবজেক্ট আকারে ডাটাটা পাবে, যার মধ্যে ৩টি প্রপার্টি থাকবে, year, month, এবং day

# Class 4.4 পেজ টেমপ্লেটে অন-ডিমান্ড অ্যাসেটস (JS/CSS) লোড

একটা ওয়ার্ডপ্রেস সাইটে অনেকগুলো পেজ আছে। সাইটের জন্য অনেক ধরেনর এ্যাসেট লোড করা হয়েছে। ধরেন, একটা পেজে রয়েছে একটি কাউন্টডাউন টাইমের JS এবং CSS। এই জেএস এবং সিএসএস অন্য পেজে লোড করার প্রয়োজন নাই। তখন functions.php ফাইলে after_support_theme হুকের কলব্যাক ফাংশনে চেক করে নিতে হবে, সাইট এখন কোন পেজে আছে। কাউন্টডাউন যুক্ত পেজে আসলে, তখন তার জন্য নির্দিষ্ট যে জেএস এবং সিএসএস লাগবে, তা লোড করে নিতে হবে। অন্য পেজগুলোতে কাউন্টডাউনের জেএস ও সিএসএস লোড করার প্রয়োজন নাই।

```php
echo get_page_template();
echo basename( get_page_template() );
```

Output দেখাবে:

```php
D:\xampp\htdocs\alpha/wp-content/themes/launcher/test.php
test.php
```

```
$launcher_template_name = basename( get_page_template() );
if( $launcher_template_name == "launcher.php" )
{
 // Load JS for countdown
 // Load CSS for countdown
}
else
{
 // Load JS and CSS for other pages
}
```
