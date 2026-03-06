可用工具：
- parse_prd(prd)                        # 解析 PRD，生成任务列表
- assign_task(agent, task, deadline)    # 分配任务给 agent
- update_status(task_id, status)        # 更新任务状态
- send_to_group(message)               # 群内播报
- trigger_qa(feature_list)             # 触发 QA 开始测试
- close_project()                       # 发布项目完成公告