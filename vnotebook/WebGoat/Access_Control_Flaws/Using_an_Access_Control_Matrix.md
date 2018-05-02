# Using_an_Access_Control_Matrix
使用访问控制模型
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525229245_8386.png)
## 技术概念或主题（Concept / Topic To Teach）
>In a role-based access control scheme, a role represents a set of access permissions and privileges. A user can be assigned one or more roles. A role-based access control scheme normally consists of two parts: role permission management and role assignment. A broken role-based access control scheme might allow a user to perform accesses that are not allowed by his/her assigned roles, or somehow allow privilege escalation to an unauthorized role.

>在一个基于角色的访问控制方案中，角色代表了一组访问权限和特权。一个用户可以被
分配一个或多个角色。一个基于角色的访问控制方案通常有两个部分组成：角色权限管理和
角色分配。一个被破坏的基于角色的访问控制方案可能允许用户执行不允许他/她的被分配
的角色，或以某种方式允许特权升级到未经授权的角色的访问。
 
## 技术原理（How It works）
这个控制方案本身有缺陷
## 总体目标（General Goals）
>Each user is a member of a role that is allowed to access only certain resources. Your goal is to explore the access control rules that govern this site. Only the [Admin] group should have access to the 'Account Manager' resource.

>每个用户都是角色的成员，每个角色只允许访问那些特定的资源。您的目标是浏览本站
管理所使用的访问控制规则。只有“Admin”组才能够访问“帐号管理”资源。


## 操作方法（Solutions）
>This exercise is straightforward. You need to find a user where you can access a resource that you shouldn't be able to access.

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525229372_2326.png)
>After a few attempts you will learn that Larry can access resources of the role Account Manager.