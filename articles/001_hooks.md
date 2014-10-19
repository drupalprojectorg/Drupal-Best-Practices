Drupal最佳实践: 创建自己的钩子
==========================

Drupal的钩子系统允许和模块交互并改变其他模块的逻辑，甚至是改变Drupal核心逻辑。这是一个非常简单的系统，甚至可以让第三方模块创建自己的钩子。在通常的实践中，有两种类型的钩子你可能想要创建，一种是内容修改类的钩子，一种是拦截类的钩子。修改类的钩子提供了一个标准的方法来修改某个特定对象或变量的内容，典型的是使用 `drupal_alter()`函数。拦截类的钩子可以让第三方模块在模块执行过程中根据条件做出一些动作。

## 例1：简单调用

``` php
<?php
  // will call all modules implementing hook_hook_name
  module_invoke_all('hook_name');
?>
```

## 例2：聚合结果

``` php
<?php
  $result = array();
  foreach (module_implements('hook_name') as $module) {
    // will call all modules implementing hook_hook_name and 
    // push the results onto the $result array
    $result[] = module_invoke($module, 'hook_name');
  }
?>
```

## 例3：使用 `drupal_alter()` 改变内容

``` php
<?php
  $data = array(
    'key1' => 'value1',
    'key2' => 'value2',
  );
  // will call all modules implementing hook_my_data_alter
  drupal_alter('my_data', $data);
?>
```

## 例4：引用传参，不能使用 module_invoke

``` php
<?php
  // @see user_module_invoke()
  foreach (module_implements('hook_name') as $module) {
    $function = $module . '_hook_name';
    // will call all modules implementing hook_hook_name
    // and can pass each argument as reference determined
    // by the function declaration
    $function($arg1, $arg2);
  }
?>
```

原文链接：http://www.erikwebb.net/blog/drupal-design-patterns/hooks