---
title:	Post View Counter Without Plugins
categories: Wordpress-Tricks
description: This simple snippet tracks and shows WordPress post views in your templates – and without the need for a plugin

pubDate: 2018.10.17
heroImage: '../../assets/wp.png'
category: 'Wordpress Tricks'
tags: ['Wordpress', 'tricks']
draft: False
---
## Introduction
Use the following snippet to track post views on your wordpress blog. The first thing you want to do is add this snippet to the functions.php of your wordpress theme. Follow step 1. and step 2. to track and display the number of views for each post. Updated this snippet from my original post on March 3rd, 2011 to Included a option for Fragment Caching so this snippet will work even on cached pages.
``` php
function getPostViews($postID){
    $count_key = 'post_views_count';
    $count = get_post_meta($postID, $count_key, true);
    if($count==''){
        delete_post_meta($postID, $count_key);
        add_post_meta($postID, $count_key, '0');
        return "0 View";
    }
    return $count.' Views';
}
function setPostViews($postID) {
    $count_key = 'post_views_count';
    $count = get_post_meta($postID, $count_key, true);
    if($count==''){
        $count = 0;
        delete_post_meta($postID, $count_key);
        add_post_meta($postID, $count_key, '0');
    }else{
        $count++;
        update_post_meta($postID, $count_key, $count);
    }
}
 
// Remove issues with prefetching adding extra views
remove_action( 'wp_head', 'adjacent_posts_rel_link_wp_head', 10, 0); 

```

## Step 1

This part of the tracking views snippet will set the post views. Just place this snippet below within the single.php inside the wordpress loop.

``` php
    <?php
              setPostViews(get_the_ID());
    ?>
```
   

### Fragment Caching

`Note:` If you are using a caching plugin like W3 Total Cache, the method above to set views will not work as the setPostViews() function would never run. However W3 Total Cache has a feature called fragment caching. Instead of the above use the following so the setPostViews() will run just fine. Tracking all your post views even when you have caching enabled.

``` php
<!-- mfunc setPostViews(get_the_ID()); --><!-- /mfunc -->
```

## Step 2
The snippet below is optional, so use this if you would like to display the number of views within your posts. Place this snippet within the loop of Single.php or Index.php
```php
<?php 
          echo getPostViews(get_the_ID());
?>
```
## Optional

This is a optional if you wants to set an View Icon near view counter. Visit [View Icons](https://developer.wordpress.org/resource/dashicons/#visibility) and copy and paste the html code and  shown below.

```html
	<p>
		<span class="dashicons dashicons-visibility"></span>
		<?php echo getPostViews(get_the_ID()); ?>
 	</p>

```



