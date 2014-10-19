Drupal最佳实践：充分使用菜单系统
===========================

Drupal的菜单系统给大多数人的印象往往是，菜单在缓存中，为了让链接生效，要先清缓存。是的，没错，但同时其也是一个强大的框架，不仅仅映射你的URL，还能给菜单添加上下文。

## load函数

下面的菜单项从URL接收一个参数，即数据的ID：

``` php
<?php
  $item['my-module/%my_module_data'] = array(
    'title' => 'My path',
    'page callback' => 'my_module_path',
    'page arguments' => array(1),
  );
?>
```

当URL被访问时，Drupal会在参数名的后面加上 `_load` 来调用其load函数：

``` php
<?php
  function my_module_data_load($my_data) {
    // lookup $my_data index
    return $my_data_object;
  }
?>
```

现在你的页面回调函数能够接收到完整的，已加载的对象，而不仅仅是从URL得到的ID：

``` php
<?php
  function my_module_path($my_data_object) {
    // object available, passed through load
    // load function my_modula_data_load
  }
?>
```

原文链接：http://www.erikwebb.net/blog/drupal-design-patterns/menus