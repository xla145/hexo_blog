---
title: 关于executeUpdate方法返回的值问题
date: 2019-03-15 09:50:30
tags:
- spring
---
今天在项目中出现了一个问题，delete 语句使用executeUpdate方法后，如果是更新一条数则返回1，但是更新一条以上数据就出现返回0的问题，我一直以为executeUpdate返回更新的条数是错误的？带着疑问我们查看源码：
```
@Override
	public int update(final String sql) throws DataAccessException {
		Assert.notNull(sql, "SQL must not be null");
		if (logger.isDebugEnabled()) {
			logger.debug("Executing SQL update [" + sql + "]");
		}

		class UpdateStatementCallback implements StatementCallback<Integer>, SqlProvider {
			@Override
			public Integer doInStatement(Statement stmt) throws SQLException {
				int rows = stmt.executeUpdate(sql);
				if (logger.isDebugEnabled()) {
					logger.debug("SQL update affected " + rows + " rows");
				}
				return rows;
			}
			@Override
			public String getSql() {
				return sql;
			}
		}

		return execute(new UpdateStatementCallback());
	}
```
从源码上看，update方法返回的就是 stmt.executeUpdate(sql) 方法返回的条数，那么上述更新多条出现0的情况是什么问题？但是执行update语句的话返回的值就是正确的，难道是执行delete语句后数据被删除了，则返回空了？那之前我执行删除一条数据为啥返回1呢？没道理呀。。。。我们继续深入分析，原来是自己上层调用的方法已经将数据删除了，所以再次执行执行delete语句返回0，对自己的用法充满无奈。。。。。。。。。。。。。。
# 总结
executeUpdate 方法返回的是被更新条数，如果是删除语句则是被删除的条数




