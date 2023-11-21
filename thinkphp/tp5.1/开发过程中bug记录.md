##  关于软删除一直没有删除的问题

20230323

> 突然间看见**tapd**提了一个严重的测试服bug,就是预约列表旧预约数据没有删除,通过检查逻辑发现
>
> 是因为软删除没有删除成功,软删除字段没有标记删除

###  **错误的写法**

```php
  public static function deleteReserve($id)
    {
        return OrderServiceReserveModel::where(function ($query) use ($id) {
            if (is_array($id)) $query->where('id', 'in', $id);
            if (!is_array($id)) $query->where('id', $id);
        })
            ->delete();
    }
```



### 原因

> 可以发现底层源码中都是通过this关键字进行删除,所以一定要得到当前的对象

```php
    /**
     * 删除当前的记录
     * @access public
     * @return bool
     */
    public function delete($force = false)
    {
        if (!$this->isExists() || false === $this->trigger('before_delete', $this)) {
            return false;
        }

        $force = $force ?: $this->isForce();
        $name  = $this->getDeleteTimeField();

        if ($name && !$force) {
            // 软删除
            $this->data($name, $this->autoWriteTimestamp($name));

            $result = $this->isUpdate()->withEvent(false)->save();

            $this->withEvent(true);
        } else {
            // 读取更新条件
            $where = $this->getWhere();

            // 删除当前模型数据
            $result = $this->db(false)
                ->where($where)
                ->removeOption('soft_delete')
                ->delete();
        }
```





### 正确的写法

> 查询到当前实例后再次循环删除

```php
    /**
     * 删除预约
     * @param int|array $id
     * @return bool
     * @throws \Exception
     */
    public static function deleteReserve($id)
    {
        $list = OrderServiceReserveModel::where(function ($query) use ($id) {
            if (is_array($id)) $query->where('id', 'in', $id);
            if (!is_array($id)) $query->where('id', $id);
        })
            ->select();
        if (empty($list)) return true;
        foreach ($list as $reserve) {
            $reserve->delete();
        }
        return true;
    }
```

