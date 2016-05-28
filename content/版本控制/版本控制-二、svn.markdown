---
title: [版本控制] 二、SVN
layout: page
date: 2015-06-17 19:29
---

[TOC]

> 我們這次版本控制主要學的是 Git, 但是 SVN 也是常用的版本控制方式之一, 所以簡單的過一下, 熟悉下它的操作.

![](http://i58.tinypic.com/2qcmu0w.jpg)

# 服務端安裝

首先, 你得有台電腦作為服務器… 好吧, 這是廢話~

這裡以 Windows 系統為例, 別問我為什麼是 Windows, 而不是 Red Hat、Mac 或者 Ubuntu, 屌絲的世界你不懂…

先去 apache 官網下載 VisualSVN.exe, 即 Windows 下的圖形化 SVN 服務器管理工具.

安裝完成過後, 可以為它添加 Repository 和 用戶 以提供給客戶端使用.

除了可以用 VisualSVN 來管理服務端外, 還可以使用 TortoiseSVN, TortoiseSVN 自帶版本褲管理, 不過不怎麼好用, 還是推薦 VisualSVN.

# 客戶端使用

當我們拿到一個 SVN Repository 的地址和賬號後, 就可以在客戶端使用了.

安装客户端软件, VisualSVN 雖然也提供了 Client 版本, 但是推薦使用 TortoiseSVN 作為客戶端軟件, 這也是使用最廣的客戶端軟件, 它除了 SVN 版本, 還有 TortoiseGIT, 另外不習慣英文的可以下載網上的中文包使用.

客戶端的作用操作有 Check Out(檢出)、Add(添加)、Commit(提交)、Update(更新)、版本回溯、Branch(分支)、比較等, 下面通過視頻來幫助記憶, 也可參考文檔說明: [http://www.uml.org.cn/pzgl/200904246.asp](http://www.uml.org.cn/pzgl/200904246.asp)

另外, 推薦使用 beyond compare 來代替 TortoiseSVN 的比較工具, 安裝 beyond compare 好後, 在 TortoiseSVN 裡設置下即可.

![](http://i59.tinypic.com/24o82ld.jpg)


# 解決衝突

考慮一種情況, 假如一個項目, 有 A 和 B 兩人共同完成, 他們同一時間都 check out 了一份庫版本號為 1 的源碼, 裡面有份 test.cpp 的源碼, 然後都做了修改, A 先提交了一次, 此時, 版本號提升為 2, 接著 B 再提交, 此時就會出現版本庫不一致無法提交, 解決方法就是 B 先從版本庫更新一下最新的源碼, 然後再和自己現有版本的源碼進行合并, 再提交, 幸好, SVN 它提供了自動合併功能;

但是需要注意, SVN 自動合併的前提是 A 和 B 修改的 test.cpp 不是同一行代碼或者修改的不是同一個文件時才會生效, 如果他們修改的是同一行, 就會導致版本合併不成功, SVN 報衝突警告, 這個時候呢, 就會使用到手動合併.

當出現衝突時, 本地庫裡就會多出三份文件, test.cpp.mine、test.cpp.r5 和 tets.cpp.r6, 第一份文件保存的是本地文件內容, 第二份文件是本地庫修改前的文件內容, 第三個是當前遠程倉庫的版本的文件內容, 然後 test.cpp 裡面的內容也會發生變化, 它保存的是兩個版本的差異信息, 我們要做的就是根據這個差異信息修改 test.cpp 文件, 修改完成後, 在本地庫上右鍵 『SVN』–>『解決』即可.

最後來說下版本庫的遷移

- 原服务器A, VisualSVN Server Manager
- 现服务器B, VisualSVN Server Manager

步骤:

- 拷贝A上Repositories文件夹到B上
- 打开B上VisualSVN Server Manager
- 右击Repositories，所有任务中选择**import existing repository**
- 选择步骤1中的项目文件夹进行导入，直到所有项目导入完成
- 替换authz和htpasswd文件，使得服务器B上VisualSVN Server Manager中的用户及用户权限跟之前的一致
