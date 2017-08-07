---
title: Git Rebase
layout: page
date: 2017-08-07 13:00
---

[TOC]

rebase 比较复杂, 应该是 git 中的高级应用了, 所以单独拿出来做篇 wiki, 转自: [ihower blog](https://ihower.tw/blog).

# 使用 git rebase 避免無謂的 merge
`git pull` 預設的行為是將遠端的 repo. 與本地的 repo. 合併, 這也是 DVCS 的初衷, 將兩個 branch 合併. 但是, 很多時候會發生以下這種情形:

![](http://wiki.smallcpp.cn/static/images/gitRebase/git-`merge`.jpg)

這是因為, 我們團隊的開發模式是本地的 branch 和遠端的 branch 會同步地非常頻繁(通常就是同名稱的 branch, 例如 master), 這兩個 branch 幾乎是完全同步. 這時候就會發現這些 `merge` 動作其實沒有必要, 會造成線圖無謂的複雜. 這時候, 會推薦使用以下這個指令:

```
git pull --`rebase`
```
<br>
加上 `rebase` 的意思是:

1. 把本地 repo. 從上次 pull 之後的變更暫存起來
2. 回復到上次 pull 時的情況
3. 套用遠端的變更
4. 最後再套用剛暫存下來的本地變更


畫圖說明一下好了, 假設合併前是這樣:

```
      D---E master
     /
A---B---C---F origin/master
```
<br>
使用 `merge` 合併後:

```
      D--------E
     /          \
A---B---C---F----G   master, origin/master
```
<br>
如果是 `rebase` 的方式, 就不會有 `G` 合併點:

```
A---B---C---F---D'---E'   master, origin/master
```
<br>
注意到, 其中 `D’`, `E’` 的 commit SHA 序號跟本來 `D`, `E` 是不同的, 因為算是砍掉重新 `commit` 了.

你會問說, 有 **conflict** 怎麼辦?

`rebase` 跟 `merge` 類似, 出現 conflict 一會暫停 `rebase` 動作, 需要你手動修復後, 然後才可以繼續動作. 這也是 `rebase` 比 `merge` 複雜一點的地方:

- `merge` 如果發生 conflict, 你只需要解決衝突一次, 然後 commit 出去就完成了.
- `rebase` 的 conflict 可能會發生在上述步驟 4 的每一次重新套用上, 所以可能需要解決衝突好幾次 (`rebase` 時所謂的解決衝突, 其實是直接修改你之前的變更內容, 所以上圖中變成 `D’` 跟 `E’`).

所以到底何時該用 `merge`? 何時可以 `rebase`? 你可能心理也有答案了, 如果你修改比較多, 預期會有較多的 conflict, 建議用 `merge` (不過, 如果是多次大範圍的主題式修改, 那是不是應該一開始就多開一個 branch 來做呢?). 如果修改範圍較小, 不太預期有 conflict, 則建議可以加上 `rebase` 參數.

如果想要把 `rebase` 當做 `git pull` 的預設值, 可以在專案的 `.git/config` 加上:

```
[branch "master"]
  remote = origin
  merge = refs/heads/master
  rebase = true
```
<br>
也可以直接加到 `~/.gitconfig` 讓所有的 tracked branches 都自動套用這個設定:

```
[branch]
  autosetuprebase = always
```
<br>
# 重建 commit
`rebase` 的真正潛力是, 我們可以從指定的版號之後, 重新隨你意 commit 一次來重建 history, 超威的. 首先輸入 `git rebase -i` 版號 就會可以跳出 editor 可以編輯, 我們可以:

1. 變更 commit 順序
1. 將多個 commit 合併 squash
1. 將一個 commit 打散 (edit 會停著讓你可以 `git reset HEAD^` 打散重新 commit, 完成後 `git rebase –continue`)

另一種 `rebase` 用法是不需要打 `-i`, 直接指定另一個 branch 或 tag, 這樣就會重新 commit 另一個 branch 的東西, 然後才 commit 自己的 (也就是上面那段的用法).

`git rebase` 若有 conflict 就會停下來, 跟 `merge` 一樣處理完 add, 然後 `git rebase –continue` 就會繼續 commit (也可以 `–skip` 或 `–abort` 放棄啦)

`rebase` 有個 onto 參數用法, 使用的情境是: 假設你有三個有 dependency 的 branch 分別叫做 master/contact/search, 後來發現 search branch 只有 depend on matser, 於是你可以輸入 `git rebase –onto master contact search` 這樣就會讓 search branch 從 master 的地方開始重新 commit.

再次提醒, `rebase` 千萬只能適合東西還沒 push 的情境, 或是你自己的 local 專用私人 branch. `rebase` 一個已經 push 出去的 repository, 然後你又把修改的 history push 出去, 是會造成超級大災難的.

在學習 `rebase` 的過程中, 很容易拿來跟 `merge` 比較一下. 我發現一個有趣的不同點: 如果有檔案在要被 merged 的 branch 中被刪除, 如果用 `rebase` 檔案最後會不存在, 但是用 `merge` 的話檔案最後還在.

另外, 在 `rebase` branch 之後, 如果再做 `merge`, 就會發現因為 master 直接就是被 `merge` 的祖先, 所以線圖直接變成一條線, 而有這種 parent 關係的 `merge` 就叫做 fast-forward. 換句話說, 因為沒有發生任何 `merge commit`, 也不會發生 conflict, Git 內部單純只是變更 reference 參照, 所以謂之 fast-forward.

好心提醒, 因為開 local branch 是如此便宜無害, 所以要做 `rebase` 時建議您可以先開一個 local branch 來實驗 `rebase`. 老實說, `rebase` 還挺危險的.

最後, `rebase` 我認為算是 Git 初學者最難理解的功能吧, 但是如果不知道什麼是 `rebase`, 就不能說是懂 Git 啊.
