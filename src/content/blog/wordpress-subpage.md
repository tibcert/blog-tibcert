---
title:	Create a Sub-Page
description: This Instruction will guide you to make a sub page menu list rather than conventional dropdown menu items.

pubDate: 2018.10.17
heroImage: '../../assets/sub-page.jpg'
category: 'Wordpress Tricks'
tags: ['Wordpress', 'tricks']
---
![image](/images/gellary/sub-page.jpg)
## Introduction

This Instruction will guide you to make a sub page menu list rather than conventional dropdown menu items.
### Post
Inside your `post.php` file in child theme,copy the lines below and paste them before post or right after the main menu. These lines of codes will display the subpage menus.
``` php
    <?php
    $args = array (
        ‘ child_of ’=> get_top_ancestor_id(),
        ‘ title_li ’ => ‘’
    );
    ?>
    <?php 
        wp_list_pages($args);
?>
```  

`Optional`: If you wants to display the parent page link, you can simply add these code in menu.
```html
    <span>
        <a href=” <?php echo get_the_permalink(get_top_ancestor_id())?>”> 
        <?php echo get_the_title(get_top_ancestor_id());>
    </a>
    </span>
```
>>  `get_top_ancestor_id()` method will invoke a functions from the functions.php file which we can define in next step.

## functions
`finctions.php:`In this file we are going to define the `get_top_ancestor_id()` functions which will fetch all the pages name under the parent page and returns them.  
Now copy the lines of code below and paste inside you child-theme functions.php file.

```php
//get top menu ancestors
Function get_top_ancestor_id(){
Global $post;
    if($post->post_parent){
        $ancestors = array_reverse( get_post_ancestors($post->ID) );
        Return $ancestors[0];
}
Return $post->ID;
}

//Does pages have childrens ?
Function has_children(){
global $post;
    $pages = get_pages(‘child_of=’ .$post->ID);
Return count($pages);
}

```
  
`NOTE`: If your pages doesn't have sub pages, Please follow these steps. 

### page.php
inside page.php paste the code where you wants to displays the menus. It actually checks if the current page has parent page or not. If it doesn't have parent page than it will skip fetching parent page menu. Otherwise it will list out all the pages list. 
```php
// If there is no sub Menus:
    <?php
    if( has_children() OR $post->post_parent > 0){
    $args = array (
            ‘ child_of ’=> get_top_ancestor_id(),
            ‘ title_li ’ => ‘’
    );
        wp_list_pages($args);
    }   
?>
```
