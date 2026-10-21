# 独立技能库与子模块接入

## 仓库关系

`mosaico-dev-skills` 是技能正文、参考和离线验证的唯一维护位置。
Echoy 通过一个 Git submodule 固定引用它，而不是复制内容。GitHub 上
`mosaico-dev-skills @ <commit>` 条目可跳转到独立库的对应版本。

Echoy 保留两种不同用途的接入：

- 旧 `skills/mosaico-development` 技能分支更新为**入口分支**，顶层只保留
  `.gitmodules` 和 `mosaico-dev-skills` gitlink。旧文件与提交历史仍在 Git 中。
- 基于实际产品 `master` 的本地 `feat/mosaico-dev-skills-submodule` 分支保留
  原工程，新增根目录模块和6项工程技能链接。这才是供产品后续合入的改动。

旧技能分支是独立技能历史，不直接把它合入产品。产品接入不继承归档工作树
中的应用代码，也不修改其他工作树、设备、依赖、默认分支或产品 tag。
3项体验／设计技能仍手动选用，原 experimental/not-run 状态不变。

## 克隆与读取

在已记录这个 submodule 的消费工程根目录：

```sh
git submodule update --init --recursive
```

检查 `git submodule status` 与父项目 gitlink 一致，再按任务读取模块内的
`README.md`、`.agents/skills/<name>/SKILL.md` 及其参考。客户端是否自动
发现 symlink 需要单独核实，不影响显式按路径读取。

典型产品工作树：

```text
Echoy/
  .gitmodules
  mosaico-dev-skills/          # 独立远程库；固定commit
  .agents/skills/
    mosaico-gsp-parity -> ../../mosaico-dev-skills/.agents/skills/mosaico-gsp-parity
    ...                       # 其余工程技能同理
  projects/                   # 原有产品文件不变
```

## 升级不会自动发生

普通 clone／update 检出父项目固定的提交。即使 `.gitmodules` 指定 `main`，
上游有新提交也不会自动改变父项目记录的版本。新技能不应无提示进入已验证
产品。需要更新时，在消费工程工作分支中执行：

```sh
git submodule update --remote --checkout mosaico-dev-skills
# 审阅上游差异并在模块目录运行已有验证
python3 -m unittest discover -s mosaico-dev-skills/tests -v
git add mosaico-dev-skills
git commit -m "chore: update shared Mosaico skills"
```

`--remote` 主动选择上游分支版本；父项目的新 gitlink 提交才记录升级。
先处理未提交模块修改，不能用强制更新丢弃本地工作。新项目首次接入时用
`git submodule add`，已有模块不要重复 add。未经授权，不自动 push、开 PR
或合入默认分支。

## 先前本地成果

最初 `chore/shared-dev-skills` 的相对本地 URL 和独立库8958188，是当时
本地克隆测试的有效记录；它们不是当前远端发布身份。旧文件副本版分支
2008b2e也保留在历史中。只用实际发布的库 commit 和当前 `.gitmodules`
核对子模块，不因为文件内容相同而混用不同 Git 历史的提交。
