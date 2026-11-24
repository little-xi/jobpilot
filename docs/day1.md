# W1 Day1 任务清单 · 2026-11-24（周二）

> 目标：**今天结束时，仓库能跑、数据能查、LLM 能调、JD 库有货。**
> 不写 Agent 循环、不碰 RAG、不碰 LangGraph。今天是地基日。

---

## Block A｜算法 1.5h（09:00–10:30）

- [ ] LeetCode 1. 两数之和
- [ ] LeetCode 49. 字母异位词分组
- [ ] LeetCode 128. 最长连续序列

要求：先自己想 10 分钟，**想不出立刻看题解**，然后关掉题解手敲一遍。
每题在 `notes/algo.md` 写一行：这题的「破题信号」是什么（如「需要 O(1) 查找 → 哈希」）。

> 今天不追求做完，追求建立「每天 3 题 + 写破题信号」的节奏。

---

## Block B｜主线 4.5h：环境 + LLM 打通 + 数据入库

### B1 环境（30min）
- [ ] 装 Python 3.11+，确认 `python --version`
- [ ] `python -m venv .venv` → 激活 → `pip install -r requirements.txt`
- [ ] `copy .env.example .env`，填入真实 API Key
- [ ] `git init`，加 `.gitignore`（`.env`、`.venv`、`*.db`、`__pycache__`），首次 commit

### B2 LLM 连通性（1h）
- [ ] 写 `scripts/check_llm.py`：加载 .env → 调一次 chat.completions → 打印返回内容和 `usage`
- [ ] 故意把 API Key 改错，观察报什么错 → 把「常见报错 → 原因」记进 `notes/day1.md`
      （401 / 404 model not found / 429 / timeout 各代表什么）

**验收**：脚本能打印一段中文回答 + input/output token 数。

### B3 摸清 API 的形状（1h，这是 Day3 做 Function Calling 的伏笔）
- [ ] 用同一个脚本试三件事，观察差异：
      1. 改 `temperature` 0 vs 1.2，看输出稳定性
      2. 加 `system` 消息设定角色，看效果
      3. 传 `response_format={"type": "json_object"}`，让它输出 JSON，观察会不会跑偏
- [ ] 记一句结论：**LLM 的输出默认不可靠，后面所有工程手段（schema 约束、重试、校验）都是为这个服务的。**

### B4 数据入库（2h）
- [ ] 打开 `data/jobs.json`（已有 2 条脱敏示例，照这个格式），**手动整理 10–20 条真实 JD**
      - 来源：你自己正在看的岗位，复制 JD 正文 → 粘贴进 json（不要爬虫，不要复制联系方式）
      - 至少覆盖 3 家不同类型：大厂 / 中厂 / 创业公司
- [ ] 写 `app/models.py`：`jobs` 表（id, company, title, city, salary, jd_text, source, url, created_at）
- [ ] 写 `app/db.py`：engine + session + `create_all`
- [ ] 写 `scripts/load_jobs.py`：读 json → 去重（按 company+title）→ 写入 SQLite
- [ ] 跑通后验证：`sqlite3` 或一段 20 行脚本查出「所有提到 LangGraph 的岗位」

**验收**：数据库里有 10+ 条岗位，能按关键词查出来。

---

## Block C｜项目 2h（19:00–21:00）

- [ ] 把 README 里的「背景」部分按自己的话改一遍（不要照抄，改成你自己的处境）
- [ ] 补 `README` 的「本地运行」章节，确保别人 clone 下来能跑（自己换个目录试一次）
- [ ] 建 `notes/` 目录，写第一天的三行复盘：
      1. 今天卡在哪
      2. 怎么解的
      3. 为什么这么选（如「为什么用 SQLite 不用 MySQL」）
- [ ] `git commit -m "day1: 环境 + LLM 连通 + JD 入库"`
- [ ] push 到 GitHub（先 private 也行，public 前检查有没有泄露 Key 和个人信息）

---

## 今日验收（全部打勾才算过）

- [ ] `.venv` 能激活，`pip list` 里能看到 fastapi / sqlalchemy / openai
- [ ] `python scripts/check_llm.py` 打印中文回答 + token 用量
- [ ] SQLite 里 ≥10 条真实 JD，能按关键词检索
- [ ] GitHub 有仓库，README 有背景 + 运行方式
- [ ] 算法 3 题过完，`notes/algo.md` 有 3 条破题信号
- [ ] `notes/day1.md` 有「常见报错 → 原因」对照表

---

## 今天必须回答自己的 3 个问题（写进 notes，W4 写简历时直接用）

1. **我整理 JD 的时候，哪些词反复出现？**（这直接决定 W2–W3 项目要做哪些功能——让项目去贴 JD，而不是做完再想怎么包装）
2. **我那份简历，扔进这 10 条 JD 里，明显缺的是什么？**（是「没做过 RAG」，还是「做过但说不清效果」？两者补法完全不同）
3. **为什么是 SQLite 而不是 MySQL？为什么先做关键词检索而不是直接上向量？**（答不出说明是在抄教程，不是在做项目）

---

## 今天绝对不要做

- ❌ 不要写 ReAct 循环（明天的事）
- ❌ 不要接 Function Calling（后天的事）
- ❌ 不要装 chromadb / langgraph（W2、W3 的事）
- ❌ 不要为了让 JD 库「看起来多」而去爬站或复制整段带联系方式的原文
- ❌ 不要在算法上花超过 1.5h
