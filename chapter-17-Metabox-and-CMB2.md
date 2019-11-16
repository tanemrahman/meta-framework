## ১৭ - মেটাবক্স এবং সিএমবি২ (CMB2)

### ১৭.১ - CMB2 এর সাথে পরিচয়, ইনস্টলেশন এবং কোড জেনারেটর

CMB2 দিয়ে কাজ করতে হলে সব কিছুই কোড দিয়ে লিখতে হয়। তবে, আশার কথা হলো, হাসিন হায়দার ভাইয়ের একটা মেটাবক্স জেনারেটর আছে, যেটা দিয়ে খুব সহজেই সিএমবি২ এর মেটা বক্সের কোড জেনারেট করা যায়। এই কোড জেনারেট করার পর inc ফোল্ডারে cmb2-mb.php নামে একটি ফাইল তৈরী করে তার মধ্যে কোডগুলো পেস্ট করে প্রয়োজনীয় মডিফিকেশন করে নিজের মত কাট-ছাট করে নিতে হবে।

এরপর cmb2-mb.php ফাইলটিকে functions.php ফাইলের এই উপরের দিকে সুবিধামত জায়গায় require_once ফাংশন দিয়ে লোড করে নিতে হবে।

CMB2 Code Generator by Hasin Hayder: http://willthemoor.github.io/cmb2-metabox-generator/

### ১৭.২ - CMB2 মেটাবক্স তৈরি করা এবং মেটা ভ্যালু ডিসপ্লে করা

ACF দিয়ে যে Image Information মেটা ইনফো গ্রুপ বানানো হয়েছিল, সেটাই CMB2 দিয়ে কিভাবে বানানো যায়, সেটাই দেখানো হয়েছে। তবে, ACF এর ক্ষেত্রে post type এর সাথে Image Information মেটাবক্সকে টগল করার যে ফিচার ছিল, তা সিএমবি২ তে নাই। ১৭.২ ভিডিওতে এই ফিচারটা জাভাস্ক্রিপ্ট দিয়ে ইমপ্লিমেন্ট করা দেখানো হয়েছে।

এছাড়াও, "Licensed?" একটা চেকবক্স আছে, যেটাতে ক্লিক করলে License Information নামে একটি টেক্সএরিয়া দেখাবে। এই রকম কন্ডিশনাল কন্ট্রোল দেখানোর জন্য "CMB2 Conditional" নামের একটি GitHub Plugin ইন্সটল করতে হবে। (এই ফিচারটা অনেকটা CodeStar এর কন্ডিশনাল dependency এর মত)।

single.php ফাইলে যেখানে মেটাফিল্ডের আউটপুট দেখানো হবে, সেখানে class_exists('CMB2') দিয়ে চেক করে নিতে হবে, সিএমবি২ ইন্সটল ও এ্যাকটিভ আছে কি না।

আর, সিএমবি২ এর মেটাইনফো পুল করার জন্য নিচের কোড ব্যবহার করতে হবে:

```php
get_post_meta(get_the_ID, "_alpha_camera_model", true);
```

### ১৭.৩ - জাভাস্ক্রিপ্টের সাহায্যে CMB2 মেটাবক্সে ইন্টারঅ্যাকটিভিটি 

এই ভিডিওতে পোস্ট ফরম্যাটের সিলেকশনের উপরে ভিত্তিকে মেটা ফিল্ড গ্রুপকে কিভাবে টগল করা যায়, তা দেখানো হয়েছে। এ্যাডমিনে admin.js নামের একটা আলাদা জেএস ফাইলকে ইনকিউ করে এতে ইমেজ পোস্ট টাইপ ক্লিক করলেই কেবল Image Information ফিল্ড গ্রুপটা দেখাবে। এর জন্য নিচের জেএস কোড ব্যবহার করা হয়েছে।

নিচের ফাংশন টি admin.js ফাইল এ যোগ করতে হবে।

```javascript
;(function($){
	$(document).ready(function(){
		$("#post-formats-select .post-format").on("click", function(){
			if( $(this).attr("id") == "post-format-image" ){
				$("#_alpha_image_information").show();
			} else {
				$("#_alpha_image_information").hide();
			}
		});

		if ( alpha_pf.format != "image" ) {
			$("#_alpha_image_information").hide();
		}
	});
})(jQuery);
```

নিচের ফাংশন টি functions.php ফাইল এ যোগ করতে হবে।

```php
function romereo_admin_assets($hook) {
	 if ( isset( $_REQUEST['post'] ) || isset( $_REQUEST['post_ID'] ) ) {
        $post_id = empty( $_REQUEST['post_ID'] ) ? $_REQUEST['post'] : $_REQUEST['post_ID'];
    }
    if ( "post.php" == $hook ) {
        $post_format = get_post_format($post_id);
        wp_enqueue_script( "admin-js", get_theme_file_uri( "/assets/js/admin.js" ), array( "jquery" ), VERSION, true );
        wp_localize_script("admin-js","romereo_pf",array("format"=>$post_format));
    }
}
add_action( "admin_enqueue_scripts", "romereo_admin_assets");
```

### ১৭.৪ - CMB2 ইমেজ ফিল্ড নিয়ে বিস্তারিত

এই পর্বে সিএমবি২ এর file uploader কন্ট্রোল নিয়ে আলোচনা করা হয়েছে। উদাহরণে মূলত ইমেজ আপলোড করা ও ইমেজকে ওয়েবসাইটে দেখানো হয়েছে।

CMB2 তে ফিল্ড এর নামের শেষে **"_id"** যুক্ত করে দিলেই মিডিয়া (ইমেজ) **ফাইলের আইডি** পাওয়া যাবে

ওয়েবসাইটে ইমেজ দেখানোর জন্য যে কোড তা নিম্নরূপ:

```php
<p>
  <?php
  // Video ১৭.৪ - CMB2 ইমেজ ফিল্ড নিয়ে বিস্তারিত
  // CMB2 তে ফিল্ড এর নামের শেষে "_id" যুক্ত করে দিলেই মিডিয়া (ইমেজ) ফাইলের আইডি পাওয়া যাবে
  $alpha_image = get_post_meta( get_the_ID(), "_alpha_image_id", true );
  //print_r($alpha_image);
  $alpha_image_details = wp_get_attachment_image_src( $alpha_image, "alpha-square" );
  echo '<img src="' . esc_url( $alpha_image_details[0] ) . '" alt="" />';
  ?>
</p>
```

### ১৭.৫ - CMB2 ফাইল আপলোড ফিল্ড নিয়ে বিস্তারিত

CMB2 তে আপলোড ফিচারের জন্য file টাইপের কন্ট্রোল ব্যবহার করা হয়। কোডের মাধ্যমে আপলোড বাটনের ক্যাপশন, কি কি ধরণের ফাইল আপলোড করতে পারবে তার রেস্ট্রিকশন, ফাইল সিলেক্ট করার পর সেই ফাইলের path দেখাবে কি না, ইত্যাদি নিয়ন্ত্রণ করা যায়।

```php
$cmb->add_field( array(
	'name' => __( 'Upload Resume', 'alpha' ),
	'id' => $prefix . 'resume',
	'type' => 'file',
	// Change upload button text
	'text' => array(
		'add_upload_file_text' => __('Upload PDF file', 'alpha'),
	),
	// Only allow PDF files
	'query_args' => array(
		'type' => array( 'application/pdf' )
	),
	'options' => array(
		'url' => false
	)
) );
```

আপলোড করা ফাইলকে ফ্রন্টএন্ডে দেখানোর জন্য নীচের কোড ব্যবহার করা যেতে পারে:

```php
<p>
	<?php
	// Video ১৭.৫ - CMB2 ফাইল আপলোড ফিল্ড নিয়ে বিস্তারিত
	$alpha_pdf_file = get_post_meta( get_the_ID(), "_alpha_resume", true);
	if( !empty($alpha_pdf_file)):
	echo "Download: " . '<a href="'. $alpha_pdf_file .'" download>PDF</a>';
	endif;
	?>
</p>
```

### ১৭.৬ - CMB2 তে রিপিটেবল ফিল্ডের সাহায্যে পাঁচমিনিটে প্রাইসিং টেবিল তৈরী করা

রিপিটেবল ফিল্ড সিএমবি২ তে বিনামূল্যের একটি ফিচার। ACF এ এটা পেইড ফিচার। সিএমবি২ তে প্রাইসিং টেবিল তৈরী করার জন্য একটি পেজে ব্যবহারের জন্য আগে কোড তৈরী করে নিতে হবে। এর জন্য CMB2 Metabox Generator ওয়েবসাইটের সাহায্যে **রেগুলার** মেটাবক্স মার্কআপ জেনারেট করে নিতে হবে। এরপরে এই কোড কে গ্রুপ হিসাবে মডিফাই করে নিতে হবে।

```php
add_action( 'cmb2_init', 'cmb2_add_pricingtable' );
function cmb2_add_pricingtable() {

	$prefix = '_alpha_pt_';

	$cmb = new_cmb2_box( array(
		'id'           => $prefix . 'pricing_table',
		'title'        => __( 'Pricing Table', 'alpha' ),
		'object_types' => array( 'page' ),
		'context'      => 'normal',
		'priority'     => 'default',
	) );

	$group = $cmb->add_field( array(
		'name' => __( 'Pricing Table', 'alpha' ),
		'id' => $prefix . 'pricing_table',
		'type' => 'group',
	) );

	$cmb->add_group_field( $group, array(
		'name' => __( 'Caption', 'alpha' ),
		'id' => $prefix . 'pricing_caption',
		'type' => 'text',
	) );
	$cmb->add_group_field( $group, array(
		'name' => __( 'Pricing Option', 'alpha' ),
		'id' => $prefix . 'pricing_option',
		'type' => 'text',
		'repeatable' => true
	) );
	$cmb->add_group_field( $group, array(
		'name' => __( 'Price', 'alpha' ),
		'id' => $prefix . 'price',
		'type' => 'text',
	) );
}
```

CMB2 Metabox Code Generator: http://willthemoor.github.io/cmb2-metabox-generator/

### ১৭.৭ - CMB2 দিয়ে খুব সহজেই একটা রিপিটেবল কনটেন্ট ব্লক (যেমন সার্ভিস) বানানো

এই পর্বে সিএমবি২ এর রিপিটেবল কনটেন্ট ব্লক দিয়ে একটি সার্ভিস পেজ বানানো দেখানো হয়েছে। এই পর্বটি ১৭.৬ এর মতই।
