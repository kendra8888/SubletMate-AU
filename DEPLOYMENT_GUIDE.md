# SubletMate AU v8 上架说明

## 当前版本是什么

这是一个前端原型，包含：
- member/ 用户端
- admin/ 管理员端
- shared/ 共享样式和模拟数据

当前用 localStorage 模拟联动。正式上线需要把 localStorage 换成 Firebase。

## 上架第一步：GitHub

1. 注册 GitHub
2. 新建 repository：subletmate
3. 上传本文件夹全部内容
4. 确认根目录有 index.html、member/、admin/、shared/

## 上架第二步：Vercel

1. 注册 Vercel
2. Continue with GitHub
3. Import subletmate repository
4. Framework 选择 Other
5. Deploy

上线后你会得到一个 vercel.app 地址。

## 上架第三步：Firebase

创建 Firebase Project 后开启：
- Authentication: Email/Password
- Firestore Database: 存 users/listings/applications/roommateProfiles/matchRequests
- Storage: 存 Student ID、Lease、Bond、Consent 等文件

## 正式联动逻辑

用户端：
- 注册/登录
- 提交房源：Firestore 创建 listings，status = pending
- 上传材料：Storage 存文件，Firestore 保存 filePath
- 填写室友资料：Firestore 写 roommateProfiles

管理员端：
- 只有 role=admin 的账号可访问
- 读取 pending listings
- 查看 Storage 文件
- 勾选材料审核结果
- 调用 Scam Score 规则生成 score/risk/reasons
- 更新 listing status = approved/rejected

用户端实时读取 Firestore：
- status 从 pending 变 approved 后，前台自动显示
- Scam Score 自动展示
- 室友匹配资料保存后，读取 roommateProfiles 并计算匹配度

## 推荐域名结构

前期：
- subletmate.com/member
- subletmate.com/admin

正式：
- subletmate.com 用户端
- admin.subletmate.com 管理员端

## Firebase 数据结构建议

users/{uid}
- email
- role: member/admin
- createdAt

listings/{listingId}
- ownerId
- title/suburb/rent/type/desc
- status: pending/approved/rejected
- scamScore/risk/reasons
- needRoommate
- roommateReq

listingDocuments/{listingId}
- studentIdPath
- photoIdPath
- leasePath
- consentPath
- bondPath
- photosPaths
- inspectionPath
- utilityPath

roommateProfiles/{uid}
- name/age/gender/school/major/suburb
- sleep/clean/cook/smoke/pet/guests/bio

applications/{applicationId}
- listingId
- applicantId
- message
- status

matchRequests/{requestId}
- fromUid
- toUid
- score
- status
