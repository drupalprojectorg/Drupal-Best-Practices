Drupal最佳实践：利用Drupal的缓存
============================

当一些内容在不同的请求中不发生改变时（注意这和静态变量是不同的），Drupal的缓存系统是一个简单好用的方法，可以减少站点的负载，并提供了一个灵活的方法可以配置数据存储的时间。通过使用 `cache_get()` 和 `cache_set()` 函数，我们用一种优雅的方式提高了整个应用的性能。

首先，让我们先看一下 `cache_set()` 函数的参数：

``` php
<?php
function cache_set($cid, $data, $bin = 'cache', $expire = CACHE_PERMANENT) {
  /* ... */
}
?>
```

## 例1：简单地使用缓存的写法

``` php
<?php
  $value = cache_get('cache_key');
  if (FALSE == $value) {
    // generate $value
    cache_set('cache_key', $value);
  }
?>
```

## 例2：创建自己的缓存数据表

``` php
<?php
  /* @file my_module.install */
  function my_module_schema() {
    $schema['cache_my_cache'] = drupal_get_schema_unprocessed('system', 'cache');
    $schema['cache_my_cache']['description'] = 'A cache table for my module.';
    return $schema;
  }
?>
```

``` php
<?php
  /* @file my_module.module */
  function my_module_flush_caches() {
    return array('cache_my_cache');
  }
?>
```

原文链接：http://www.erikwebb.net/blog/drupal-design-patterns/cache