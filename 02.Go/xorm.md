# postgres

+ jsonb 可以直接写成map映射的方式
+ 乐观锁是添加version字段，xorm会自动把version字段值对用version++ 语句。修改时也会带上version条件，如果不满足修改行数为0 重新执行