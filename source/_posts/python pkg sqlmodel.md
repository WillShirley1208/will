---
title: python sqlmodel&SQLAlchemy
date: 2023-11-14 15:28:51
tags: problems
categories: python
---

## 单个实体转换为字典

- 方式一

```python
# 将实例对象转换为字典，排除未设置值的字段
entity_dict = entity.dict(exclude_unset=True)
```

> 使用 Pydantic 模型对象的一个方法，用于将模型对象转换为字典形式。`exclude_unset=True` 参数是用来控制是否排除未设置的属性字段。当设置为 `True` 时，将排除那些未设置值的字段，即只包含已经设置值的字段。

- 方式二：

  遍历字典（对没有设置的字段进行默认值设置，设置排查某字段放入字典）

```python
for key, value in user_group_data.dict(exclude_unset=True, exclude={"team_roles"}).items():
	if value is not None:
	setattr(user_group, key, value)
```

- 方式三

```python
resourceCreate: ResourceCreate
...
resource = Resource(**resourceCreate.dict())
```



## 单个实体与实体转换

```shell
role_update: RoleUpdate
...
role = Role.from_orm(role_update)
```

## 列表实体转换为字典列表

- 方式一 **session.query** 

> 在直接返回 查询结构的时候，优先使用，可以直接把结果转为json（e.g. res_json = jsonable_encoder(res_data)）

```python
db_rel_usergroup_roles = (session.query(RelUserGroupUser.user_id, Role.id, Role.name, Role.nick_name, Role.label)
                             .join(RelUsergroupRole, RelUserGroupUser.usergroup_id == RelUsergroupRole.usergroup_id)
                             .join(Role, RelUsergroupRole.role_id == Role.id)
                             .filter(RelUserGroupUser.user_id == user_id)
                             .all())
        rel_usergroup_roles_info = []
        for rel_usergroup_role in db_rel_usergroup_roles:
            rel_usergroup_roles_info.append(Role(
                id=rel_usergroup_role[1],
                name=rel_usergroup_role[2],
                nick_name=rel_usergroup_role[3],
                label=rel_usergroup_role[4]
            ))
        return rel_usergroup_roles_info
```

- 方式二 **session.exec**

> 在需要特殊处理查询的不同字段信息时，优先使用此方法

```python
rel_usergroup_user_list = session.exec(
            select(RelUserGroupUser.usergroup_id, RelUserGroupUser.user_id, UserGroup.name, UserGroup.label, UserGroup.remark)
            .where(RelUserGroupUser.user_id == user_id)
            .join(UserGroup, UserGroup.id == RelUserGroupUser.usergroup_id)
        ).all()
        converted_list = []
        for row in rel_usergroup_user_list:
            converted_obj = RelUserGroupUserRead(
                user_id=row.user_id,
                usergroup_id=row.usergroup_id,
                usergroup_name=row.name,
                usergroup_label=row.label
            )
            converted_list.append(converted_obj)
        return converted_list
```

- 

## 查询过滤

- 筛选条件为或条件

- ```python
  resource_list = session.query(Resource).filter(or_(Resource.type == "menu", Resource.type == "element")).all()
  ```

## update操作

如果提前有 `session.get(entity, id)`，那后续在修改相关表属性`enitty.colx = xxx` 之后，需要紧接`session.add(entity)`

### session的获取

在使用 SQLModel 进行数据库操作时，可以使用上下文管理器 `with Session(engine) as session` 来创建并管理数据库会话。这样可以确保会话在使用完毕后会被正确关闭，释放数据库连接资源。

在例如 `async def delete_user(*, user_id: int, session: Session = Depends(get_session)):`中，`session` 参数是通过 `Depends(get_session)` 进行依赖注入的，FastAPI 框架会负责在请求处理函数执行完毕后自动关闭会话。所以在请求逻辑中，你不需要手动关闭会话连接。

## in_语法使用



## session.exec vs session.query

在SQLAlchemy中，`session.query`和`session.exec`都可以用来执行SQL查询。然而，它们之间有一些关键的区别。

`session.query`是SQLAlchemy ORM的一部分，它返回的是模型对象。这意味着你可以使用Python的属性和方法来访问和操作返回的数据。这对于编写面向对象的代码非常有用。

```python
users = session.query(User).filter(User.name == 'John').all()
for user in users:
    print(user.id, user.name)
```

另一方面，`session.exec`是SQLAlchemy Core的一部分，它返回的是原始的SQL结果。这意味着你需要使用列名作为字符串来访问返回的数据。这对于执行复杂的SQL查询或者需要更接近SQL的性能优化可能更有用。

```python
result = session.exec(select(User).where(User.name == 'John'))
for row in result:
    print(row['id'], row['name'])
```

总的来说:

如果正在编写面向对象的代码，或者查询相对简单，那么`session.query`可能是更好的选择。

如果需要执行复杂的SQL查询，或者需要更接近SQL的性能优化，那么`session.exec`可能是更好的选择。