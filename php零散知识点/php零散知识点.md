## [php如何给调用的方法添加横线标识以后不维护了](https://docs.phpdoc.org/guide/references/phpdoc/tags/deprecated.html#deprecated)

给调用的方法添加横线标识是一种简单有效的方式来标记该方法已经不再维护。这种方式通常被称为“弃用标记”。在 PHP 中，可以使用 `@deprecated` 注解来标记弃用的方法。

在 PHP 中，@deprecated 注解可以添加到一个类、函数或方法的注释块中，如下所示：

```php
/**
 * @deprecated 该方法已经废弃，请使用新的方法来代替。
 */
public function oldMethod() {
    // 方法实现
}
```



当使用该方法时，PHP 会在运行时发出警告，通知开发者该方法已经被弃用，并且推荐使用新的方法来代替它。这样可以防止开发者意外地使用不再维护的方法，同时也可以帮助开发者更好地管理代码库和实现迭代更新。

# [php 注释](https://docs.phpdoc.org/guide/references/phpdoc/tags/index.html)