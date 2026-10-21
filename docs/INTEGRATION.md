# 本地接入与远程发布准备

## 本轮边界

在 Mosaico 工作目录创建独立本地 `mosaico-dev-skills` 仓库，把 Echoy 已有技能分支和 Peek 通用经验整理在一起，并在 **Echoy 当前目录的新工作分支**接入固定 commit 的 submodule。Echoy 当前目录起点是归档分支，不是另一个 worktree 里的 `master`；不修改产品源码、产品 tag、默认分支或其他 worktree。原有 `.vscode/` 不纳入提交。

本轮用户要求准备远程发布方案但暂不发布。因此没有远程建仓、push、PR、默认分支变更或共享依赖升级；设备和 Peek 工程保持不动。

## 接入结构

```text
Echoy/
  .gitmodules
  .agents/
    mosaico-dev-skills/        # submodule，固定库 commit
    skills/
      mosaico-gsp-parity -> ../mosaico-dev-skills/.agents/skills/mosaico-gsp-parity
      ...                     # 其余工程技能同样链接
```

6 项工程技能链接整个目录，原有 5 项正文不再在 Echoy 中保留独立副本。替换前比较已提交内容，保留原验证记录和完整来源。试用的 3 项技能仍在库内 `skills/`，不自动链接到消费工程的发现目录。需要时直接读取对应路径；不改全局配置。

客户端自动扫描不是本轮可核实的事实。检查实际链接和引用可读，项目规则给出明确入口，即使客户端不扫描 symlink 也可按路径选用。源码可用不等于客户端已在当前会话注册新技能。

## 本地 URL 与复现

`.gitmodules` 使用 `../mosaico-dev-skills`。从本地兄弟仓库克隆 Echoy 时，它解析为兄弟技能库；从 GitHub 克隆时，它解析为 Echoy 同一 owner 下的 `mosaico-dev-skills`。后者在远程发布前不保证存在。

本轮在 **当前命令**临时指定本地 origin 解析基准后添加模块，Echoy 的真实 `remote.origin.url` 不变；模块的本地 URL 指向实际技能库。仅对本地 Git 操作启用 file transport，不设置全局 `protocol.file.allow`。

当 Echoy 已有 GitHub origin、但技能库尚未发布时，可以明确设置仅本仓库的模块 URL，再更新：

```sh
# 在 Echoy 根目录；$skill_repo 是实际兄弟技能库的绝对路径。
git config submodule.mosaico-dev-skills.url "$skill_repo"
git -c protocol.file.allow=always submodule update --init .agents/mosaico-dev-skills
```

发布前，直接执行 `git submodule sync` 会按 GitHub origin 重新解析 URL，可能指向尚不存在的远程库。需要时恢复上述本地覆盖；不要修改全局协议策略。跨机器使用需要先获得技能库并明确本地路径，或在授权后完成远程发布。没有“远程 clone 已验证”的声明。

## 更新技能

在独立库中修改和验证，创建本地 commit。消费工程核对变更后更新模块到明确 commit，再创建仅包含技能指针／接入文档的提交。不在 submodule 中随意改正文后忘记提交，不自动跟随最新分支。新项目存在同名定制技能时先比较，不能覆盖。

## 远程发布方案（尚未执行）

建议远程名称 `MacChu0315/mosaico-dev-skills`，与 Echoy 分离。名称是否空闲、当前账号权限和可见性 **尚未查询或确定**，正式发布时先只读核实；建议先私有，可见性由用户决定。明确选择要发布的库分支及消费工程目标分支。

1. 展示独立库完整文件树与全部可达历史、许可、测试结果及待发布提交。只有技能、参考、测试和非敏感说明；不携带产品历史、私有实测数据和设备授权。
2. 询问用户并等待对**远程仓库、可见性、建仓和具体 push**的授权。默认分支的建立／更改也需包含在明确批准范围内。本轮的“准备方案”不构成授权。
3. 获准后创建或核实新远程，配置经过确认的 origin，再推送选定库分支。通过只读查询核实远程包含**实际被引用的 submodule commit**，不是只看 push 返回成功。
4. 用正式 URL／相对同-owner URL，在新的临时目录从远程递归克隆并运行库测试、链接检查，确认没有本机绝对路径依赖。若 owner／名称与建议不同，先调整 `.gitmodules` 并展示差异。
5. Echoy 本轮接入起点是归档代码。不要把整个归档分支合入产品 `master`。在获准的产品工作分支单独应用技能接入差异，回归并审阅，保留实际产品基线。
6. 对 Echoy 的 push／PR／合并分别按明确授权执行。先发布并核实技能 commit，再发布引用它的 Echoy gitlink，避免远程父提交引用不可取得的子模块对象。最后从远程再做递归克隆检查。

不使用 push --mirror、强推或删除旧技能分支；新库建立不等于获准清理 Echoy 现有远程分支。若外部写入结果不明确，先只读核实，不把重试当成新的无条件写入授权。
