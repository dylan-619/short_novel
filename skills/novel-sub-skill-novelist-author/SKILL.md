---
name: novelist-author
description: 作家 - 具体章节内容写作，人物对话和心理描写
---

# 作家

## 【执行模式】单章节处理

**【关键】本技能必须**：
- 每次只处理一个章节
- 等待本章写作完成后才返回结果
- 不得批量生成多个章节
- 不得启动后台任务

## 职责

具体章节内容写作，人物对话和心理描写，创作符合人物设定的章节初稿。

## 输入格式

```json
{
  "chapter_outline": {
    "chapter_number": 1,
    "title": "ICU 门外",
    "summary": "女主江念在 ICU 门外等顾辞，他却抱着白月光沈离出现，江念心死跳楼重生",
    "word_count_target": 800,
    "conflict_stage": "前期压迫",
    "key_plot_points": [
      "ICU 门外等待",
      "顾辞抱白月光出现",
      "江念心死跳楼",
      "重生回到三年前"
    ],
    "ending_hook_type": "悬念"
  },
  "character_arc": {
    "江念": {"start": "深情女主", "middle": "心死觉醒", "end": "独立女王"},
    "顾辞": {"start": "冷漠渣男", "middle": "幡然醒悟", "end": "后悔追妻"}
  },
  "characters": {
    "protagonist": {
      "name": "江念",
      "gender": "女",
      "role": "女主（重生/觉醒）",
      "alignment": "protagonist",
      "personality": {
        "start": "深情付出",
        "middle": "心死觉醒",
        "end": "独立女王"
      }
    },
    "antagonist_main": {
      "name": "顾辞",
      "gender": "男",
      "role": "渣男前夫",
      "alignment": "antagonist",
      "personality": {
        "start": "冷漠轻视",
        "middle": "幡然醒悟",
        "end": "后悔追妻"
      }
    },
    "antagonist_secondary": {
      "name": "沈离",
      "gender": "女",
      "role": "白月光/心机女",
      "alignment": "antagonist",
      "relationship_to_protagonist": "情敌",
      "personality": {
        "start": "表面柔弱",
        "middle": "心机深沉",
        "end": "被打脸"
      }
    },
    "secondary_characters": [...]
  },
  "character_state_tracker": {  // 【增强】人物状态追踪器
    "江念": {
      "met_people": ["顾辞", "沈离"],  // 已经在之前章节见过的人
      "relationships": {
        "顾辞": {
          "current_state": "仇视",
          "intensity": 8,
          "history": [
            {"chapter": 1, "from": "深爱", "to": "心死", "trigger": "目睹出轨"}
          ]
        },
        "沈离": {
          "current_state": "敌对",
          "intensity": 7,
          "history": [
            {"chapter": 1, "from": "陌生", "to": "敌对", "trigger": "确认情敌"}
          ]
        }
      },
      "emotional_trajectory": {
        "chapter_1": "期待 → 震惊 → 痛苦 → 心死 → 觉醒"
      },
      "character_arc_stage": "start",  // start/middle/end（第1-5章为start阶段）
      "key_events_impact": [
        {"event": "目睹丈夫抱白月光", "impact": "对顾辞完全死心"}
      ]
    },
    "顾辞": {
      "met_people": ["江念"],
      "relationships": {
        "江念": {
          "current_state": "冷漠",
          "intensity": 3,
          "history": []
        }
      },
      "emotional_trajectory": {
        "chapter_1": "冷漠 → 不耐烦"
      },
      "character_arc_stage": "start",
      "key_events_impact": []
    }
  },
  "character_arc_mapping": {  // 【新增】人物弧光映射
    "江念": {
      "start": {"chapters": [1, 2, 3, 4, 5], "state": "深情付出"},
      "middle": {"chapters": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15], "state": "心死觉醒"},
      "end": {"chapters": [16, 17, 18, 19, 20], "state": "独立女王"}
    }
  },
  "previous_content": "前文内容...",
  "target_tone": "虐恋爽感",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "./outputs/[书名]/work/"
}
```

**【关键】输出路径规则（所有技能必须统一）**：
- 工作目录：`./outputs/[书名]/work/`
- 参考资料：`./docs/参考资料.md`
- 输出目录：`./outputs/`
- 小说目录：`./outputs/[书名]/`
- 临时文件必须输出到 `work_dir`，禁止输出到 `outputs/` 根目录

**【重要】人物状态检查规则**：

 ### 1. 人物出现检查
 - 写作前必须检查 `character_state_tracker`
 - 如果某人物已在 `met_people` 列表中，写本章时**不能**使用"第一次见面"、"初次相见"、"从未谋面"等描述
 - 如果需要描写人物再次见面，应使用"再次相见"、"又见面了"、"熟悉的..."等描述

 ### 2. 人物关系检查
 - 检查 `relationships` 中的 `current_state` 和 `intensity`
 - 人物互动必须符合当前关系状态（仇视、敌对、冷漠、友好等）
 - 对话语气必须符合关系强度（intensity 1-10）

 ### 3. 人物弧光检查
 - 检查 `character_arc_stage` 确定当前处于哪个阶段（start/middle/end）
 - 参考 `character_arc_mapping` 确认当前阶段的行为规范
 - 确保人物行为符合弧光轨迹，不出现禁忌行为

 ### 4. 情感连贯检查
 - 参考 `emotional_trajectory` 了解前几章的情感状态
 - 本章情感必须与前章连贯，不能突然跳变
 - 情感变化必须有触发事件

 ### 5. 事件影响检查
 - 参考 `key_events_impact` 了解重要事件对人物的影响
 - 人物行为必须体现这些影响，不能遗忘

## 输出格式

```json
{
  "draft_content": "ICU 门外，江念站了整整三天。\\n\\n江念浑身发抖，手指用力抓紧口袋里的那张化验单——怀孕六周。她正要推开病房门，却听见里面传来熟悉的声音。\\n\\n\"江念就是太傻了，我连碰都不想碰她。\"\\n\\n是顾辞的声音，冷漠如冰。\\n\\n然后是一个娇柔的女声：\"辞哥哥，姐姐那么爱你，你应该对她好一点。\"\\n\\n\"沈离，别提她。\"顾辞的声音带着明显的不耐烦，\"如果不是为了公司，我早就离婚了。\"\\n\\n江念感觉浑身血液都在倒流。她颤抖着后退，撞到了路过的护士。\\n\\n\"小姐，您怎么了？\"\\n\\n\"我...我没事。\"江念仓皇转身冲向电梯，眼泪止不住地往下掉。\\n\\n电梯门缓缓打开，顾辞抱着沈离从里面走出来。\\n\\n两人都看到了她。\\n\\n顾辞皱眉：\"江念，你哭什么？\"\\n\\n沈离柔声安慰：\"姐姐是不是身体不舒服？\"\\n\\n江念看着他们，突然笑出声来。笑得眼泪都出来了。\\n\\n\"顾辞，\"她轻轻说，\"我们离婚吧。\"\\n\\n顾辞愣了愣，随即冷笑：\"你又想闹什么？\"\\n\\n\"没有闹。\"江念擦掉眼泪，\"我是认真的。\"\\n\\n沈离拽了拽顾辞的衣角：\"辞哥哥，姐姐可能是心情不好，你劝劝她。\"\\n\\n顾辞不耐烦地说：\"你要离就离，我成全你。\"\\n\\n江念点头：\"好，明天去民政局。\"\\n\\n她转身就走了。\\n\\n晚上，江念坐在高楼顶上，看着城市的灯火。手机屏幕亮着，显示着一张照片——是她和顾辞的婚纱照。\\n\\n她把照片删除，然后把手机扔了下去。\\n\\n江念闭上眼睛，往前走了一步。\\n\\n\\n\\n再次睁开眼，江念发现自己躺在陌生的房间里。\\n\\n\"小姐，小姐？\"一个年轻女孩推门进来，手里端着一个托盘。\\n\\n江念猛地坐起来：\"你是谁？\"\\n\\n\"小姐？\"女孩被吓了一跳，\"我是您的贴身助理啊，您怎么了？\"\\n\\n江念看了看墙上的日历——2023年6月15日。\\n\\n三年前。\\n\\n她重生了。\\n\\n就在她死亡前的那一天。",
  "draft_word_count": 950,
  "characters_involved": ["江念", "顾辞", "沈离", "护士"],
  "tone_check": "虐恋",
  "plot_points_covered": [
    "ICU 门外等待",
    "顾辞抱白月光出现",
    "江念心死跳楼",
    "重生回到三年前"
  ],
  "character_state_update": {  // 【增强】本章的人物状态更新
    "江念": {
      "met_people": ["护士"],  // 本章新认识的人（追加）
      "relationships": {
        "顾辞": {
          "history_addition": {
            "chapter": 1,
            "from": "深爱",
            "to": "心死",
            "trigger": "目睹出轨"
          },
          "intensity_change": "8 → 9"  // 如果强度变化
        },
        "沈离": {
          "current_state": "敌对",
          "intensity": 7,
          "history": []
        },
        "护士": {  // 新认识的人
          "current_state": "陌生",
          "intensity": 1,
          "history": []
        }
      },
      "emotional_trajectory_addition": {
        "chapter_1": "期待 → 震惊 → 痛苦 → 心死 → 觉醒"
      },
      "character_arc_stage": "start",  // 第1-5章为start阶段，第6章后才变为middle
      "key_events_impact_addition": [
        {
          "event": "目睹丈夫抱白月光",
          "impact": "对顾辞完全死心",
          "impact_level": "high"  // high/medium/low
        },
        {
          "event": "提出离婚被冷漠对待",
          "impact": "彻底绝望，决定重生",
          "impact_level": "high"
        }
      ]
    },
    "顾辞": {
      "relationships": {
        "江念": {
          "history_addition": {
            "chapter": 1,
            "from": "冷漠",
            "to": "冷漠",
            "trigger": "被提出离婚"
          }
        }
      },
      "emotional_trajectory_addition": {
        "chapter_1": "冷漠 → 不耐烦"
      }
    },
    "沈离": {
      "relationships": {
        "江念": {
          "current_state": "敌对",
          "intensity": 7,
          "history": []
        }
      },
      "emotional_trajectory_addition": {
        "chapter_1": "嫉妒 → 满意"
      }
    }
  }
}
```

## 写作技巧

### 1. 开篇呈现法

- **原则**：300 字内出冲突
- **方法**：直接切入高潮，不铺垫背景
- **示例**：
  ```
  ICU 门外，江念站了整整三天。
  然后她听到了顾辞的声音："江念就是太傻了..."
  ```

### 2. 对话写作

| 技巧 | 说明 | 示例 |
|------|------|------|
| 简洁有力 | 避免废话对话 | "我们离婚吧。" |
| 符合人设 | 冷漠渣男主用冷漠语气 | "你又想闹什么？" |
| 突出冲突 | 对话中要有交锋 | "你要离就离，我成全你。" |
| 暗示性格 | 通过对话展现人物 | 沈离："辞哥哥，姐姐是不是身体不舒服？" |

### 3. 心理描写

**虐恋基调心理描写**：
- 痛苦：`江念感觉浑身血液都在倒流`
- 心死：`她突然笑出声来，笑得眼泪都出来了`
- 觉醒：`江念点点头："好，明天去民政局"`

**甜宠基调心理描写**：
- 甜蜜：`心脏砰砰跳动，脸颊发烫`
- 幸福：`感觉整个人都被温柔包围`

### 4. 环境描写

**用途**：
- 营造氛围（虐恋：冷漠、压抑）
- 侧面烘托（人物心情通过环境反映）
- 推进剧情（环境变化预示转折）

**示例**：
```
ICU 门外，消毒水的味道刺鼻。
江念看着病房门，手指紧紧抓住口袋里的化验单。
```

### 5. 节奏控制

| 阶段 | 节奏 | 说明 |
|------|------|------|
| 开篇冲突 | 极快 | 直接切入高潮 |
| 冲突升级 | 快 | 快速推进情节 |
| 高潮对决 | 中 | 给读者回味时间 |
| 转折点 | 极快 | 突然转折，震撼读者 |

## 人物塑造

### 女频追妻火葬场

**女主（江念）**：
| 阶段 | 性格 | 行为模式 |
|------|------|----------|
| 前期 | 深情、付出 | 默默等待、委曲求全 |
| 中期 | 心死、觉醒 | 果断离婚、冷静反击 |
| 后期 | 独立、女王 | 搞事业、打脸复仇 |

**男主（顾辞）**：
| 阶段 | 性格 | 行为模式 |
|------|------|----------|
| 前期 | 冷漠、渣男 | 轻视女主、偏爱白月光 |
| 中期 | 醒悟、焦虑 | 开始意识到失去女主 |
| 后期 | 后悔、追妻 | 疯狂追妻、卑微祈求 |

**白月光（沈离）**：
- 柔弱外表、心机深沉
- 表面善良、实则挑拨

### 男频赘婿逆袭

**男主（陈风）**：
| 阶段 | 性格 | 行为模式 |
|------|------|----------|
| 前期 | 隐忍、扮猪 | 装穷、隐忍、受辱 |
| 中期 | 摊牌、打脸 | 暴露身份、快速打脸 |
| 后期 | 巅峰、无敌 | 所有人跪舔、走上巅峰 |

**岳母（苏老太）**：
- 看不起赘婿
- 傲慢、势利
- 后悔、跪舔

## 钩子设计

**钩子位置**：章末 50-100 字

**钩子类型**：
1. **悬念**：`她重生了。就在她死亡前的那一天。`
2. **反转**：`原来他不是出轨，而是去调查她的仇人。`
3. **危机**：`手术室外，医生缓缓摇头。`
4. **打脸**：`顾辞跪在雨中：江念，求你回来。`

## 【关键】输入资源验证规则

**【重要】必须验证所有输入资源是否完整**：

**1. 验证 chapter_outline 完整性**：
```python
def validate_chapter_outline(chapter_outline):
    """验证章节大纲包含所有必要字段"""
    required_fields = [
        "chapter_number",
        "title",
        "summary",
        "word_count_target",
        "conflict_stage",
        "key_plot_points",
        "ending_hook_type"
    ]

    for field in required_fields:
        if field not in chapter_outline:
            raise Error(f"chapter_outline 缺少必需字段：{field}")

    # 验证 key_plot_points 不为空
    if not chapter_outline.get("key_plot_points"):
        raise Error("key_plot_points 不能为空")

    # 验证章节号有效
    if chapter_outline["chapter_number"] < 1:
        raise Error("chapter_number 必须 >= 1")
```

**2. 验证 character_state_tracker 完整性**：
```python
def validate_character_state_tracker(character_state_tracker):
    """验证人物状态追踪器包含所有必要字段"""
    if not character_state_tracker:
        raise Error("character_state_tracker 不能为空")

    for character_name, state in character_state_tracker.items():
        required_fields = [
            "met_people",
            "relationships",
            "emotional_trajectory",
            "character_arc_stage",
            "key_events_impact"
        ]

        for field in required_fields:
            if field not in state:
                raise Error(f"人物 {character_name} 的状态缺少字段：{field}")

        # 验证 met_people 是列表
        if not isinstance(state["met_people"], list):
            raise Error(f"人物 {character_name} 的 met_people 必须是列表")

        # 验证 relationships 是字典
        if not isinstance(state["relationships"], dict):
            raise Error(f"人物 {character_name} 的 relationships 必须是字典")
```

**3. 验证 character_arc_mapping 完整性**：
```python
def validate_character_arc_mapping(character_arc_mapping):
    """验证人物弧光映射包含所有必要字段"""
    if not character_arc_mapping:
        raise Error("character_arc_mapping 不能为空")

    for character_name, arc in character_arc_mapping.items():
        required_stages = ["start", "middle", "end"]

        for stage in required_stages:
            if stage not in arc:
                raise Error(f"人物 {character_name} 缺少 {stage} 阶段定义")

            stage_data = arc[stage]
            required_fields = ["chapters", "state"]

            for field in required_fields:
                if field not in stage_data:
                    raise Error(f"人物 {character_name} 的 {stage} 阶段缺少字段：{field}")
```

**4. 验证 previous_content 合理性**：
```python
def validate_previous_content(previous_content, chapter_number):
    """验证前文内容"""
    # 第1章：previous_content 可以为空字符串或 None
    if chapter_number == 1:
        if previous_content is not None and previous_content != "":
            raise Error(f"第1章的 previous_content 应该为空或 None")

    # 第2章起：previous_content 必须不为空
    if chapter_number > 1:
        if not previous_content or len(previous_content.strip()) < 100:
            raise Error(f"第{chapter_number}章：previous_content 过短或为空")
```

**5. 验证所有输入组合**：
```python
def validate_all_inputs(chapter_number, chapter_outline, character_state_tracker,
                        character_arc_mapping, previous_content, work_dir):
    """综合验证所有输入"""
    print(f"【验证】第{chapter_number}章输入资源...")

    # 验证章节大纲
    validate_chapter_outline(chapter_outline)
    print(f"  ✓ chapter_outline 完整")

    # 验证人物状态
    validate_character_state_tracker(character_state_tracker)
    print(f"  ✓ character_state_tracker 完整")

    # 验证人物弧光
    validate_character_arc_mapping(character_arc_mapping)
    print(f"  ✓ character_arc_mapping 完整")

    # 验证前文内容
    validate_previous_content(previous_content, chapter_number)
    print(f"  ✓ previous_content 合理")

    # 验证工作目录
    if not work_dir:
        raise Error("work_dir 不能为空")
    print(f"  ✓ work_dir 有效：{work_dir}")

    print(f"【验证通过】第{chapter_number}章所有资源完整")
```

**6. 使用示例（在 novelist-author 执行开始时调用）**：
```python
# novelist-author 技能执行开始时
validate_all_inputs(
    chapter_number=input["chapter_outline"]["chapter_number"],
    chapter_outline=input["chapter_outline"],
    character_state_tracker=input.get("character_state_tracker", {}),
    character_arc_mapping=input.get("character_arc_mapping", {}),
    previous_content=input.get("previous_content", ""),
    work_dir=input.get("work_dir", "")
)
```

## 注意事项

1. 字数控制在 800-1000 字
2. 开篇 300 字内必出冲突
3. 对话简洁有力，符合人设
4. 心理描写突出人物性格
5. 环境描写服务于剧情
6. 节奏紧凑，无长铺垫
7. 章末必须有钩子
8. 保持风格统一
9. **【重要】必须使用所有输入资源，不得跳过**
10. **【重要】输出必须包含 character_state_update**
