# LaravelVueClass
**留言板 用户中心 TodoList**  
你需要完成一个后台系统，包括用户注册登陆，主页的留言板，以及各个用户自身的TodoList等功能。
页面需要使用ajax技术来保持无刷新使用网站各项功能。
各个页面的组成及功能如下列表所示。
##  页面构成
###	首页
- 个人中心链接
    - 未登录会跳转到登录页
- 公共留言板
    - 用户名
    - 时间
    - 留言内容
    - 删除按钮
### 注册页
- 用户名输入框
- 密码输入框
- 提交按钮
- 返回登录按钮
### 登录页
- 用户名输入框
- 密码输入框
- 登录按钮
- 前往注册按钮
### 个人中心页
- 个人用户名
- 资料（可编辑）
    - 姓名
    - 出生日期
    - 年龄（根据出生日期自动计算）
    - 简介
- 登出按钮
- 备忘录
    - 选项卡（未完成任务，已完成任务）
    - 未完成任务，任务列表
        - 单项任务完成情况（勾选移入已完成任务）
	    - 单项任务开始时间
	    - 单项任务内容描述
    - 已完成任务
	    - 已完成任务列表
	    - 清空所有已完成任务按钮

##  API
### 1. Auth
#### a. register (/register)
Description: User can register.
##### Request Method: POST  
##### Request Parameters:  
- account
- password
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for wrong data:
    - Status Code: 422
    - Body:
        - msg: "data cannot be processed"
- Data for repeat account:
    - Status Code: 422
    - Body:
        - msg: "This account has already been taken"
#### b. Login (/login)
Description: User can login.  
##### Request Method: POST  
##### Request Parameters:  
- account
- password
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
        - data:
            - account
            - nickname
            - birth
            - age
            - profile
            - token
            - is_admin ( 1 / 0 )
- Data for wrong data:
    - Status Code: 422
    - Body:
        - msg: "data cannot be processed"
- Data for wrong user credential:
    - Status Code: 401
    - Body:
        - msg: "user credentials are invalid"  
##### Body Result Sample for Success:
```json
{
  "msg": "success" ,
  "data": {
    "account": "USER1" ,
    "nickname": "NICKNAME" ,
    "birth": "2011-06-09" ,
    "age": 10 ,
    "profile": "PROFILE" ,
    "token": "AUTHORIZATION_TOKEN",
    "is_admin": 0
  }
}
```
#### c. Logout (/logout?token={token})
Description: User can logout.
##### Request Method: GET 
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized"  

#### e. Update info in user (/user?token={token})
Description:  User can update info. 
##### Request Method: PATCH  
##### Request Parameters:  
- nickname
- birth
- profile
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 

### 2. Message Board
#### a. Get Messages (/message_board?token={token})
Description: Get all messages under message board. 
##### Request Method: GET  
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
        - data
            - messages(array):
                - id
                - user_id
                - nickname
                - datetime (format：[YYYY-mm-dd HH:mm:ss])
                - content
            - mate
                - pagination
                    - current_page
                    - per_page
                    - total
                    - links
                        - previous
                        - next
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for not found:
    - Status Code: 404
    - Body:
        - msg: "not found"
##### Body Result Sample for Success:
```json
{
  "msg": "success" ,
  "data": {
    "messages": [
      {
        "id": 1 ,
        "user_id": 1 ,
        "nickname": "NICKNAME" ,
        "datetime": "2020-12-11 11:11:11",
        "content": "MESSAGE_CONTENT"
      },
      {
        "id": 2 ,
        "user_id": 2 ,
        "nickname": "NICKNAME" ,
        "datetime": "2020-12-12 11:11:11",
        "content": "MESSAGE_CONTENT"
      },
      {
        "id": 3 ,
        "user_id": 2 ,
        "nickname": "NICKNAME" ,
        "datetime": "2020-12-13 11:11:11",
        "content": "MESSAGE_CONTENT"
      }
    ],
    "meta": {
      "pagination": {
        "current_page": 2,
        "per_page": 3,
        "total": 11,
        "links": {
          "previous": "/message_board?token={token}&page_number=1&page_size=1",
          "next": "/message_board?token={token}&page_number=3&page_size=3"
        }
      }
    }
  }
}
```
#### b. Add New Message (/message?token={token})
Description: User can create any new message.
##### Request Method: POST
##### Request Parameters:  
- content
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
        - data:
            - id
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for wrong data:
    - Status Code: 422
    - Body:
        - msg: "data cannot be processed"
##### Body Result Sample for Success:
```json
{
  "msg": "success" ,
  "data": {
    "id": 1 
  }
}
``` 
#### c. Delete Message (/message/{message_id}?token={token})
Description: Users can only delete their own messages. administrator can delete any messages.
##### Request Method: DELETE   
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for not found:
    - Status Code: 404
    - Body:
        - msg: "not found"

### 3. Todo List
#### a. Get TodoList (/todo_list?token={token})
Description: Get all items under todo list. 
##### Request Method: GET  
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
        - data
            - items(array):
                - id
                - type ( "Uncompleted" / "Completed")
                - datetime (format：[YYYY-mm-dd HH:mm:ss])
                - content
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for not found:
    - Status Code: 404
    - Body:
        - msg: "not found"
##### Body Result Sample for Success:
```json
{
  "msg": "success" ,
  "data": {
    "items": [
      {
        "id": 1 ,
        "type": "Uncompleted",
        "datetime": "2020-12-11 11:11:11",
        "content": "ITEM_CONTENT"
      },
      {
        "id": 2 ,
        "type": "Completed",
        "datetime": "2020-12-12 11:11:11",
        "content": "ITEM_CONTENT"
      },
      {
        "id": 3 ,
        "type": "Uncompleted",
        "datetime": "2020-12-13 11:11:11",
        "content": "ITEM_CONTENT"
      }
    ]
  }
}
```
#### b. Add New Item (/todo_list?token={token})
Description: User can create any item in the todo list.
##### Request Method: POST
##### Request Parameters:  
- datetime (format：[YYYY-mm-dd HH:mm:ss])
- content
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
        - data:
            - id
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for wrong data:
    - Status Code: 422
    - Body:
        - msg: "data cannot be processed"
##### Body Result Sample for Success:
```json
{
  "msg": "success" ,
  "data": {
    "id": 1 
  }
}
``` 
#### c. Check To Completed (/todo_list/check/{item_id}?token={token})
Description: User can check their item as completed.
##### Request Method: get   
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
- Data for not found:
    - Status Code: 404
    - Body:
        - msg: "not found"
#### d. Delete All Completed items (/todo_list/completed/all?token={token})
Description: User can delete all their completed items.
##### Request Method: DELETE   
##### Response Result:
- Data for success:
    - Status Code: 200
    - Body:
        - msg: "success"
- Data for unauthorized:
    - Status Code: 401
    - Body:
        - msg: "unauthorized" 
