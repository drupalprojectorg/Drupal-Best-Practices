Drupal最佳实践：在数据库中存储对象
=============================

下面的两个例子都是使用如下数据结构：

``` php
<?php
  // use the following table schema
  $schema['my_table'] = array(
    'description' => 'My table.',
    'fields' => array(
      'id' => array('type' => 'serial', 'unsigned' => TRUE, 'not null' => TRUE,),
      'value' => array('type' => 'varchar', 'length' => 255, 'not null' => TRUE, 'default' => '',),
      'options' => array('type' => 'text', 'serialize' => TRUE, 'default' => '',),
    ),
    'primary key' => array('id'),
  );
?>

```

## 保存对象

不可避免地，你会需要在数据库中保存大的对象。在基本的PHP中，这需要在SQL语句或PDO对象里写出每个对象的属性。而Drupal给我们提供了几个简单的方法，可以以最小的努力保存这样的信息。首先, `drupal_write_record()` 可以直接映射类属性到字段名。当使用 `fetchObject()` (D7) 或 `db_fetch_object()` (D6) 时，你能加载对象并做一些必要的修改，然后使用 `drupal_write_record()` 保存这些修改。

### 例子：

``` php
<?php
  $my_data = new stdClass();
  $my_data->id = 1;    // remove this to see INSERT behavior
  $my_data->value = 'some value';

  if ($my_data->id) {
    // if this is an existing entry, specify table's primary key
    drupal_write_record('my_table', $my_data, array('id'));
  }
  else {
    drupal_write_record('my_table', $my_data); // $my_data->id is now set
  }
?>
```

## 序列化对象

另一种选择是，你可以在数据库中把整个对象或者数组存在一个字段里。大多数情况下用于为字段保存额外的配置选项，Schema API提供了一个架构可以在加载和卸载时从数据库分别自动地序列化和解序列化数据。而为了做到这一点，只需要为字段设置 `serialize` 属性即可。

### 例子：

``` php
<?php
  $my_data = new stdClass();
  $my_data->id = 1;    // remove this to see INSERT behavior
  $my_data->value = 'some value';
  $my_data->options = array(
    'opt1' => 'value1',
    'opt2' => 'value2',
  );

  if ($my_data->id) {
    // if this is an existing entry, specify table's primary key
    drupal_write_record('my_table', $my_data, array('id'));
  }
  else {
    drupal_write_record('my_table', $my_data);
  }
?>
```

原文链接：http://www.erikwebb.net/blog/drupal-design-patterns/objects