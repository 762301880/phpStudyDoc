# php8 跟php7比优化的地方

PHP 8 相比 PHP 7，核心优化集中在 **JIT 性能突破、类型系统现代化、语法糖降本、错误处理严谨化** 四大方向，同时底层内存与数组结构也得到显著优化。

核心优化一览（按收益排序）

| 维度     | 关键优化                  | 具体收益                                                     | 代码示例 / 配置                                              |                                    |                   |                      |
| -------- | ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------- | ----------------- | -------------------- |
| **性能** | ✨ JIT 编译器（8.0）       | CPU 密集型场景提升 **30%–50%**，计算 / 循环 / 递归加速       | `opcache.enable=1``opcache.jit=tracing``opcache.jit_buffer_size=128M` |                                    |                   |                      |
| **性能** | 数组底层重构（8.0+）      | 大型数组遍历 / 操作提速，`packed array` 分离优化缓存命中率   | —                                                            |                                    |                   |                      |
| **性能** | DateTime 组件优化（8.1+） | 构造、格式化、时区操作提速 **20%+**                          | `new DateTime("2024-01-01", timezone: 'auto')`               |                                    |                   |                      |
| **类型** | 联合类型（8.0）           | 支持 `int                                                    | string                                                       | null`，替代 PHPDoc，静态分析更精准 | `function foo(int | string $x): void {}` |
| **类型** | 命名参数（8.0）           | 无视参数顺序，只读必填项，代码自文档化                       | `htmlspecialchars($str, double_encode: false)`               |                                    |                   |                      |
| **语法** | match 表达式（8.0）       | 替代 switch，严格匹配无隐式转换，更简洁                      | `$res = match($code) { 200 => 'OK', default => 'Err' };`     |                                    |                   |                      |
| **语法** | Nullsafe 运算符（8.0）    | 链式调用 `?->`，遇 null 直接返回 null，少写 `isset`          | `$user?->profile?->name`                                     |                                    |                   |                      |
| **语法** | 构造器属性提升（8.0）     | 直接在参数定义属性，减半样板代码                             | `class Post { public function __construct(public string $title) {} }` |                                    |                   |                      |
| **错误** | 错误转异常（8.0）         | 非致命错误抛出 `Error` 异常，可统一捕获                      | `try { file_get_contents($file); } catch (ErrorException $e) {}` |                                    |                   |                      |
| **错误** | 严格比较逻辑（8.0）       | 字符串与数字比较不再隐式转换，减少隐式 bug                   | `"0" === 0` → `false`                                        |                                    |                   |                      |
| **实用** | 新字符串函数（8.0+）      | `str_contains()`/`starts_with()`/`ends_with()` 替代 `strpos` 写法 | `str_contains($text, "keyword")`                             |                                    |                   |                      |



### 关键细节说明

1. **JIT 编译器（核心性能突破）**

   PHP 8.0 引入 JIT，将高频执行的 “热点代码” 直接编译为机器码，绕过 Zend VM 解释执行。对 **CPU 密集型任务**（数学计算、循环、递归）提升显著，综合基准测试可达 **3 倍**；Web 应用（如 Laravel）通常提升 **20%–35%** 响应时间。需配合 Opcache 启用，建议 `opcache.jit=tracing` 模式。

   

2. **类型系统现代化**

   

   - **联合类型**：解决 PHP 7 仅支持单一类型的痛点，结合 PHPStan/Psalm 可提前发现类型错误。
   - **命名参数**：尤其适合参数多、默认值多的函数（如数据库查询、配置构建），降低传参顺序错误风险。
   - **严格比较**：消除 `==` 的隐式转换陷阱，提升代码健壮性。

   

3. **语法糖与开发效率**

   

   - **match**：比 switch 更简洁、严格，支持直接返回值，无需 `break`。
   - **Nullsafe**：替代多层 `isset($a['b']->c)`，减少空值判断代码。
   - **构造器属性提升**：大幅减少类定义的重复代码，尤其适合数据模型（DTO/Entity）。

   

4. **底层与实用优化**

   

   - **数组**：PHP 8 对 `packed array`（连续索引数组）做了内存布局优化，遍历和访问更快。
   - **字符串函数**：新函数语义更清晰，避免 `strpos($s, $t) !== false` 的易错写法。
   - **错误处理**：将可恢复错误转为异常，便于统一日志和异常处理，提升可维护性。

   

------

### 升级建议

1. **性能优先**：开启 JIT（`opcache.jit=tracing`），优先升级到 **8.1+**（DateTime 优化更完善）。
2. **类型安全**：启用 **严格类型**（`declare(strict_types=1)`），配合联合类型和命名参数，提升代码质量。
3. **语法降本**：逐步用 match 替代 switch，用 Nullsafe 替代 `isset`，用构造器属性提升简化类定义。
4. **注意兼容**：PHP 8 废弃了 `create_function()`、`each()` 等函数，升级前需检查代码兼容性。

------

### 总结

PHP 8 不是简单的版本升级，而是 **性能、类型、开发体验的全面现代化**。对后端开发（如 Laravel/Hyperf 项目）来说，启用 JIT 可降低服务器成本，类型安全和语法糖能减少线上 bug、提升协作效率。建议优先升级到 **8.1/8.2** 稳定版，逐步适配新特性。

# thinkphp8 环境搭建

php.ini需要打开如下几个扩展(如果没有  php.ini复制 php.ini-development  )

```php
extension_dir = "ext"
extension=openssl
extension=curl
extension=fileinfo
extension=zip
```

