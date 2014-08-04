title: 4.1 Form definitions
tags:
  - Java
date: 2013-01-01 16:55:11
---

(本文译者是子青)

### 表单的提交处理

#### 表单的定义

在`play.data`中，包含了几个helpers来处理HTTP提交的表单数据和表单数据验证。处理提交的表单的最简单方法是，定义一个 `play.data.Form`，来包装现有的类:

    public class User {
        public String email;
        public String password;
    }
    Form<User> userForm = form(User.class);

    注:底层的数据绑定均采用的是Spring的数据绑定。

    提交的表单可以从HashMap<String,String>中的数据生成`User`对象数据。

    Map<String,String> anyData = new HashMap();
    anyData.put("email", "bob@gmail.com");
    anyData.put("password", "secret");

    User user = userForm.bind(anyData).get();