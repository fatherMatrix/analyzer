# 非破坏性 Git 速查

analyzer skill 绝不能改动工作树。用以下只读命令查看任意分支、tag 或 commit。

## 查看当前状态
```bash
git remote -v                          # 远程（仓库位置）
git rev-parse --abbrev-ref HEAD        # 当前分支名
git describe --tags --always           # 最近的 tag，或短 commit
git rev-parse HEAD                      # 当前 commit 完整哈希
git status -s                          # 工作树是否有改动？
```

## 读取某个 ref 下的代码（无需 checkout）
`<ref>` 可以是分支名、tag 或 commit 哈希。

```bash
git show <ref>:<路径>                  # 打印 <ref> 下某文件的内容
git ls-tree -r --name-only <ref>       # 列出 <ref> 下的所有文件
git ls-tree --name-only <ref> <目录>/  # 列出 <ref> 下某目录的文件
git cat-file -p <ref>:<路径>           # 等价于对 blob 的 git show
```

示例：
```bash
git show v1.2.0:src/main.go            # tag v1.2.0 处的 main.go
git show origin/release:config/app.yml # 远程 release 分支上的文件
git show abc1234:lib/util.py           # 某个 commit 处的文件
git ls-tree -r --name-only v1.2.0      # v1.2.0 的完整文件列表
```

## 跨 ref 对比
```bash
git diff <refA> <refB> -- <路径>       # 跨两个 ref 对比单个文件
git diff <refA>..<refB> --stat         # 跨 ref 的变更文件概览
git log <ref> --oneline -20            # 某 ref 的近期历史
git log <refA>..<refB> --oneline       # 在 B 而不在 A 的提交
```

## 验证请求的 ref 是否存在
```bash
git rev-parse --verify <ref>           # ref 未知时返回非零退出码
git tag --list '<模式>'                # 查找匹配的 tag
git branch -a --list '<模式>'          # 查找匹配的本地/远程分支
git fetch --tags origin                # （仅在用户授权拉取时）获取最新 tag/分支
```

## 在某个 ref 上搜索代码
`grep`/`rg` 作用于工作树。要在不 checkout 的情况下搜索其他 ref，用 `git grep`：
```bash
git grep -n "模式" <ref>               # 在 <ref> 上搜索模式
git grep -n "模式" <ref> -- <路径>     # 限定到某路径
```

## 禁止（会改动工作树——绝不用于满足 ref 请求）
`git checkout`、`git switch`、`git reset`、`git stash`、`git clean`、`git merge`、`git rebase`、`git pull`。
如果用户在知情的前提下明确要求切换，那是用户的决定——但绝不要悄悄切换，也不要「为了方便分析」而切换。
