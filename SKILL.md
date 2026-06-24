---
name: pospal-create-demand
description: Create old-version Pospal/银豹 demand tickets in the ticket.pospal.cn demand form with Browser automation. Use when the user asks to新建/创建/发布/提交旧版需求单、银豹需求、Pospal demand, or gives a需求标题/描述 and wants Codex to optimize demand content, fill the old Portal demand form, apply default field values, and submit automatically after required fields are filled unless the user asks to review first.
---

# Pospal Create Demand

Use Browser to create and submit a demand ticket in the old Pospal demand form. Treat a user request to use this skill as permission to submit after required fields are filled.

## Defaults

- URL: `https://ticket.pospal.cn/pdstatic/#/add?categoryUid=1716868798932543356`
- Title prefix: automatically prepend `【新版】店铺_` to the user's title unless it already starts with that prefix or the user explicitly provides a full title.
- Latest publish date: current date + 30 days, formatted `YYYY-MM-DD`
- Decision reason: `行业需要`
- Source: `产品规划`
- Development group: `我的店铺`
- Handlers: `陈文彬`, `吴梦诗`
- Development hours: `1`
- Feishu sync URL: `https://lcnkm4pzdioe.feishu.cn/wiki/MiGjwEmOYiFQgxkXgBdc2JkTnig?table=tblJAnS25GFQ8fyW&view=vewljDKyRk`
- Feishu creator: `吴梦诗`
- Content optimization: optimize the user's demand description before filling it unless the user explicitly asks to keep the original text.
- Submit behavior: submit automatically after filling required fields; do not ask for a second confirmation unless required information is missing or the user asks to review first.
- Review: keep default `否`
- Description edit permission: keep default `所有人`
- Priority: keep default `普通`
- Type: keep default `需求`
- System invasiveness: keep default `0`

User-provided values override these defaults.

## Title Handling

- Treat the user's `标题` as the actual content by default and fill `需求标题` as `【新版】店铺_` + the user's title.
- Do not duplicate the prefix if the user title already starts with `【新版】店铺_`.
- If the user explicitly says the provided title is a full title, asks to keep the original title, or provides a different prefix, use the title as provided.

## Content Optimization

- Before filling `描述`, polish the user's provided content by default.
- Preserve the original intent, platform names, account names, constraints, and numbered requirements; do not invent scope, permissions, dates, or implementation details.
- Prefer concise product-requirement wording. When useful, structure the description as `背景：...` and `需求：...`.
- If the user says `不用优化`, `原文`, or `按我写的`, keep the description unchanged.
- If the user asks to optimize content but the provided description is too vague to preserve meaning, ask for the missing information instead of guessing.

## Workflow

1. Open the URL with Browser.
2. If the page requires login, ask the user to log in manually, then wait for them to say they are logged in.
3. Fill required content:
   - `需求标题`: apply the default title prefix rule, then fill the final title.
   - `描述`: use the optimized description by default; preserve line breaks and numbered items.
   - `最晚发布时间`: default to current date + 30 days unless the user provides a date.
   - `决策原因`: select the provided value, otherwise `行业需要`.
   - `来源`: select the provided value, otherwise `产品规划`.
4. Add one schedule row if none exists:
   - Click `+ 新增` under `排期`.
   - Select development group `我的店铺` unless overridden.
   - Fill development hours `1` unless overridden.
   - Select handlers `陈文彬` and `吴梦诗` unless overridden.
5. Leave optional fields blank unless the user provides values:
   - 标签
   - 所属行业
   - 软著产品线
   - 上线地址
   - 附件
   - 依赖关系备注
6. If all required fields are available, click `提交需求` automatically without asking for confirmation.
7. If required information is missing, ambiguous, or a page validation error appears, stop and ask the user for the missing value or correction.
8. After success, report the demand number, title, optimized description, status, development group, development hours, handlers, latest publish date, and demand detail link.
9. Ask the user whether to sync the created demand to the Feishu table unless the user already explicitly requested Feishu sync in the same message.
10. If the user confirms Feishu sync, add one record to the configured Feishu table using only these fields:
   - `需求`: `[需求编号] 需求标题`, for example `[XQ260624022] 【新版】店铺_采购退货和出库时，选择代理商支持搜索`
   - `创建人`: `吴梦诗` unless the user overrides it
   - `需求链接`: the submitted demand detail URL

## Feishu Sync

- Only sync after a demand has been successfully created and a demand detail URL is available.
- Default behavior: ask `是否同步到飞书表格？` and wait for the user's confirmation before writing to Feishu.
- If the user says `新建并同步飞书`, `创建后同步飞书`, or otherwise explicitly requests sync up front, sync after creation without asking again.
- Open the configured Feishu sync URL with Browser and use the current visible table view.
- Add a new record in the Feishu table; do not edit or delete existing records.
- Fill only `需求`, `创建人`, and `需求链接`. Leave all other fields unchanged/blank/default.
- If Feishu requires login or permission, ask the user to log in or request access, then continue after they confirm.
- If the table UI changes or the target fields cannot be identified, stop and ask the user for the exact insertion location or field mapping.

## Browser Tips

- Prefer visible UI labels and role/text locators. If a dropdown is difficult, use Browser screenshots or visible DOM to identify the exact option.
- The rich-text description editor is contenteditable; click/fill it while preserving new lines.
- The development-hours input is the number input in the schedule table, often shown with placeholder like `工时余-2.7天`.
- Handler dropdown supports multiple selections; select both names before closing it.
- If a required field is missing or unclear, fill everything else first and ask only for the missing value.

## Safety

- For new demand creation, the user's request to use this skill is permission to submit after filling required fields.
- Do not delete, cancel, or edit an existing submitted demand without explicit user confirmation.
- If the user explicitly says to review before submitting, stop before clicking `提交需求`.
- Do not enter passwords, SMS codes, or SSO verification for the user; ask the user to complete login manually.
- Do not store credentials or sensitive account details in the skill.
