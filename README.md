

## 如何在 python 中禁用来自 lambda 的默认日志消息？

How to disable default log messages from lambda in python?



**问题：是否有任何可能的方法可以禁用 CloudWatch 来记录 Lambda 函数的事件？ 如果可能，那么执行此操作的步骤是什么。**

<u>回答：无法为 lambda 函数禁用 CloudWatch 日志。 一种解决方法是您可以将以下内联策略添加到您的角色以禁用 CloudWatch 日志。 当您需要再次登录时，您可以将“拒绝”更改为“允许”。</u>

背景：如果您启用了日志，您将始终获得默认日志。 您无法禁用它们。
但是，在某些情况下，您可能希望某个特定的 Lambda 函数根本不发送日志。 您可以通过专门为该 Lambda 函数创建一个新角色来解决此问题，并且在那里没有日志记录权限。

如果您需要在日志记录和不记录之间频繁切换，您可以拥有如下的策略文件：

**（1）AWS中国区：**

```json
{
"Version": "2012-10-17",
"Statement": [
    {
        "Effect": "Deny",
        "Action": [
            "logs:CreateLogGroup",
            "logs:CreateLogStream",
            "logs:PutLogEvents"
        ],
        "Resource": [
            "arn:aws-cn:logs:*:*:*"
        ]
    }
  ]
}
```



**（2）AWS海外：**

```json
{
"Version": "2012-10-17",
"Statement": [
    {
        "Effect": "Deny",
        "Action": [
            "logs:CreateLogGroup",
            "logs:CreateLogStream",
            "logs:PutLogEvents"
        ],
        "Resource": [
            "arn:aws:logs:*:*:*"
        ]
    }
  ]
}
```



更重要的是，如何在lambda中修改权限的策略文件？

步骤如下：

**(1) 打开 Lambda的 IAM Role 权限**

Lambda 函数的执行角色是一个 AWS Identity and Access Management (IAM) 角色，用于向函数授予访问 AWS 服务和资源的权限。您在创建函数时提供该角色，当您的函数被调用时，Lambda 代入该角色。您可以创建一个有权将日志发送到 Amazon CloudWatch 并将跟踪数据上传到 AWS X-Ray 的开发执行角色。

打开 Lambda 控制台的“函数”页面。 选择函数。选择 Configuration (配置)，然后选择 Permissions (权限)。
在资源摘要下，查看函数可以访问的服务和资源。以下示例显示了当您在 Lambda 控制台中创建一个执行角色时，Lambda 向该执行角色添加的 CloudWatch Logs 权限。

![image-20211011113933091](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211011113933091.png)



**(2) 修改 Lambda的 IAM Role 权限**

![image-20211011114054584](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211011114054584.png)

修改权限为显式 deny
![image-20211011114215725](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211011114215725.png)


**(3) 修改 Lambda的 IAM Role 权限后的样子**

![image-20211011114439582](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211011114439582.png)



![image-20211011114605675](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/image-20211011114605675.png)

