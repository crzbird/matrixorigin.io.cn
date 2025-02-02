# MatrixOne 中的事务应用场景

在一个财务系统中，不同用户之间的转账是非常常见的场景，而转账在数据库中的实际操作，通常是两个步骤，首先是对一个用户的账面金额抵扣之后，然后是对另一个用户的账面金额进行增加。只有利用事务的原子性，才能确保总账面资金没有变化，同时两个用户之间的账户都完成了各自的抵扣与增加，例如 A 用户此时对 B 用户转账 50：

```
start transaction;
update accounts set balance=balance-50 where name='A';
update accounts set balance=balance+50 where name='B';
commit;
```

当两次 `update` 都成功并且最终提交，整个转账才算真正完成，任何一步的失败，必须让整个事务回滚，才能确保原子性。

此外，两个账户转账的过程中，在没有提交之前，无论是 A 或者 B，看到的都是尚未转账完成的账面余额，这是事务的隔离性。

在转账过程中，数据库会检查 A 的账面资金是否大于 50，A 和 B 在系统中是否确有其人，只有都满足这些约束，才能保证事务的一致性。

完成转账后，无论系统是否重启，数据已经完成了持久化，体现了事务的持久性。
