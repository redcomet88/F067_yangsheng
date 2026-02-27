# F067  中医养生知识图谱健康问答系统+膳食食疗系统

> 完整项目收费，可联系QQ: 81040295 微信: mmdsj186011 注明从github来的，谢谢！
也可以关注我的B站： 麦麦大数据 https://space.bilibili.com/1583208775
> 
> 关注B站，私信获取！  [麦麦大数据](https://space.bilibili.com/1583208775)
编号: F067
# F067 中医养生知识图谱健康问答系统
---
## 视频

[video(video-eQfLZGSz-1767148724503)(type-csdn)(url-https://live.csdn.net/v/embed/508162)(image-https://i-blog.csdnimg.cn/direct/18b481530a9b400d9a110ea59ce6cf73.png)(title-F067-中医养生问答科普系统)]

---

## 1 系统简介

系统简介：本系统是一个基于 **Vue+Flask+Neo4j** 构建的 **中医养生知识图谱健康问答系统**。其核心功能围绕**中医知识管理、智能问答、养生科普与可视化数据分析**。主要功能模块包括：**用户登录注册、健康问答、养生科普、药材药方搜索、知识图谱可视化、数据大屏、关键词词云分析、后端管理、个人设置**等。

---

## 2 功能设计
该系统采用前后端分离的B/S架构模式，基于Vue+Flask+MySQL+Neo4j技术栈实现。前端通过Vue.js框架搭建响应式界面，结合 **Vuetify** 组件库提供友好的用户交互体验，使用Vue-Router进行页面路由管理，Axios实现与后端的异步数据交互。Flask后端负责构建RESTful API服务，通过**SQLAlchemy操作MySQL**数据库存储用户、新闻、药材、药方等结构化数据，利用 **PyMySQL** 作为MySQL驱动支持，并通过 **py2neo** 与Neo4j图数据库交互，实现知识图谱的查询、展示与管理。
在**智能问答**功能方面，系统采用自然语言处理与图数据库联合查询机制，支持中医方剂、药材、症候等多类实体的语义检索；当知识图谱无法匹配时，调用大模型（LLM）生成专业回答；在**可视化分析**方面，结合ECharts与echarts-wordcloud组件，实现数据大屏、词云、关键词分析等动态展示。

---

### 2.1 系统架构图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/98cc9cb7137d4514b69edc79b3306f50.jpeg)

---

### 2.2 功能模块图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fc4ed19808d74cc4b9625137225e46bc.jpeg)

主要功能模块有：

1. **用户管理模块**：登录注册、权限控制、个人信息设置
2. **养生问答模块**：智能问答（中医/养生）、多模态问题处理
3. **知识图谱模块**：图谱可视化、节点关系查询
4. **养生科普模块**：资讯发布与查看、评论点赞
5. **搜索服务模块**：药材、药方、关键词检索
6. **数据分析模块**：数据大屏、词频分析、词云展示
7. **后台管理模块**：用户、药材、药方、新闻管理
8. **可视化交互模块**：D3.js图谱布局、双击/悬停等交互

---
## 3.1 登录 & 注册
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0d0f1e3fca484ba88b0b8fe342a60fe5.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c8cf36ed83574075a4bef87fd0678a19.jpeg)
登录注册做的是一个可以切换的登录注册界面，点击“去登录”或“去注册”可以切换。登录需要验证用户名和密码是否正确，支持用户名+密码认证，密码经过 **BCrypt 加密存储**。注册流程需填写用户名、密码、邮箱，注册成功后默认为普通用户（roles=normal），系统自动完成用户信息写入MySQL表 `tb_user`。

---
## 3.2 智能健康问答系统
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/311aaf5c57ce4d53ac70263390097611.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d1f9c9d174714103acd73aa389408e4c.jpeg)
系统实现多类型中医养生问答功能，涵盖：
- **养生问答**：如“菊花茶的养生功效是什么？”
- **中医病症问药方**：如“气虚外感风寒怎么治疗？”
- **药方问药材**：如“麻黄汤包含哪些药材？”
- **配伍关系查询**：如“当归配伍的药方有哪些？”
**问答逻辑流程如下**：
1. **用户输入问题**（如：“麻黄汤能治什么病？”）
2. **关键词提取**（使用自定义分词器 + 结巴分词，识别“麻黄汤”“治”“病”）
3. **路由分发**：根据关键词关键词类型调度至 `search_2`（主治症候查询）或 `search_3`（药材组成查询）
4. **Cypher查询执行**：
   ```cypher
   MATCH (n:药方 {name: '麻黄汤'})-[r:治疗]-(m:症候)
   RETURN m.name
   ```
5. **返回结果**：如“感冒、风寒表证等”
6. **若无结果**：调用大模型（LLM）生成专业回答，如：“麻黄汤主要用于风寒表实证，以发汗解表为主，常伴恶寒、无汗、头痛症状。”
---

## 3.3 养生知识图谱可视化
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8c4d5c04c07e4b4088948e5a77bcbfeb.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fa632e9ac6804facaafb560e44f5be8d.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/aead945e895b466fa103046d01c95ef1.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d262913dded841c685e9ff2317a131aa.jpeg)

系统使用 **D3.js** 实现知识图谱的可视化展示，支持以下功能：

- **节点类型区分**：使用不同颜色标识 `药方`、`药材`、`养生方法`、`典籍`、`症候` 等节点。
- **关系网络绘制**：节点通过 `包含`、`出自`、`治疗`、`适用于`、`配伍禁忌` 等关系线连接。
- **交互增强**：
   - 双击节点：弹出属性表单，展示完整信息（如药材性味归经），并支持展示关联图片。
   - 悬停关系线：高亮路径上的两个节点。
   - 双击关系线：弹窗展示关系两端节点详情。
- **布局自动化**：采用力导向图（Force-Directed Graph）自动优化节点位置，避免重叠与混乱。

---
## 3.4 药材与药方搜索
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/15e1cc280d9340228991d0dd1f81233d.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/be85e58f9cf94f019e6e6ece41d83772.jpeg)

支持通过关键词快速检索药材、药方信息：
- **药材搜索**（例：“黄芪”）：返回其性味、归经、功效、配伍禁忌等。
- **药方搜索**（例：“四物汤”）：返回组成药材、主治症候、来源典籍等。
- **多维筛选**：支持按“名称”、“来源”、“主治症候”等多字段模糊查询。

前端使用模糊输入框，通过`Axios`请求后端接口 `/search`，后端根据 `QUERY_MAP` 动态生成Cypher语句：

```python
QUERY_MAP = {
    '1': "MATCH (n:药方) WHERE n.name =~ '.*%s.*'",
    '2': "MATCH (n:药方) WHERE n.来源 =~ '.*%s.*'",
    '3': "MATCH (n:药方) WHERE n.主治症候 =~ '.*%s.*'",
    '4': "MATCH (n)-[r]-(m) WHERE n.name =~ '.*%s.*'"
}
```

---

## 3.5 养生资讯发布与查看
支持翻页、点赞、评论等功能。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/15ea9b8f57784babada0f40141f4829f.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/82dd755826354b199b3e6561e894f627.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8212de7bf61b441ab67dedfca385e531.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/abf771430ddf46e0a0fe389741eb5760.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/15fc9c2e77c440698091705767c52b86.jpeg)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2897049de64a4cad93761345926d630f.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cb6e4e8439424f589680d85b4f4fe30e.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/286b494a2cff40a6878f480cbaf1a608.jpeg)

- **资讯管理**：用户可发布养生内容（标题、正文、图片），系统记录为 `tb_news` 表数据。
- **审核机制**：
  - 普通用户发布 → `status = -1`（未审核）
  - 管理员审核 → `status = 0`（已通过）
  - 前端仅展示 `status = 0` 的新闻
- **互动功能**：
  - **点赞**：用户可对资讯点赞，记录于 `tb_thumb`，支持取消点赞
  - **评论**：在资讯页加载所有评论，支持用户发表评论（存储于 `tb_comment`）
  - **评论区**：动态加载评论，评论含用户头像、昵称、时间、内容

---
## 3.6 后台管理模块
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/00cc609e9d1f4692b9a44ff3abd92368.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5c29a4502e2a42f4bee172d3c9583370.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bbdb819999e54292b3e62cda4a2812a0.jpeg)

管理员拥有全部管理权限，包括：

- **用户管理**：增删改查用户信息，修改角色（normal/admin）
- **药材管理**：新增/编辑/删除药材信息
- **药方管理**：管理药方数据，包括名称、组成、主治等
- **新闻管理**：审核用户发布的资讯，支持批量操作
- **权限控制**：通过 `roles` 字段控制菜单可见性

前端通过 `applayout.vue` 判断用户权限，动态加载不同菜单项。

---

## 3.7 数据可视化大屏
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ab47dd4d788d4451b46c4c102db97cb8.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b267e47eac80403e8eb642c02aa4d24d.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a979daac03ab438a891bfc3c9aff19c7.jpeg)

利用 ECharts 实现数据可视化，包括：

- **统计分析**：
  - 药方类型分布饼图
  - 收录数量趋势折线图
  - 药材/药方数量排名柱状图
- **关键词分析**：
  - 使用 **jieba** 分词 + **TF-IDF** 和 **TextRank** 提取中药方剂关键词
- **词云分析**：
  - 从 `主治症候` 字段提取高频词，通过 **echarts-wordcloud** 组件生成词云图
  - 重点展示“咳嗽”、“失眠”、“脾胃虚弱”等高频症候词

---

## 3.8 个人设置
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cc80f459b53d41809d87158630ca9a92.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/510f4b52ed2049a6ad78ecfd68d92600.jpeg)

个人设置方面包含了用户信息修改、密码修改功能。

- **用户信息修改**：可以上传头像（支持JPG/PNG），填写昵称、性别、邮箱等个人信息。
- **修改密码**：需输入旧密码，验证成功后可设置新密码，密码使用 **BCrypt** 加密存储。

---

## 3.9 知识图谱图谱模型
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/665d4007197c4b008f235fa37616311c.jpeg)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4ac1d4ef757d4280badabd5c61b0f045.jpeg)

### 2.1 核心节点类型

| **节点标签** | **属性示例** | **说明** |
|--------------|----------------|----------|
| 药方         | name, 来源, 组成, 主治症候 | 经典中药方剂 |
| 药材         | name, 性味, 归经 | 单味药材信息 |
| 养生方法     | name, 适用季节, 功效 | 食疗/运动等养生方案 |
| 典籍         | name, 朝代, 作者 | 《黄帝内经》等医学典籍 |
| 症候         | name, 病理特征 | 气血两虚等中医症候 |

### 2.2 主要关系类型

```
(药方)-[包含]->(药材)
(药方)-[出自]->(典籍)
(药方)-[治疗]->(症候)
(养生方法)-[适用于]->(症候)
(药材)-[配伍禁忌]->(药材)
```

---

## 4 程序核心算法代码

### 4.1 代码说明

系统后端核心为问答模块与图谱查询，采用 **Flask** 构建API，**py2neo** 操作 **Neo4j**，实现动态 Cypher 查询。主要函数包括：

- `get_target_array()`：基于结巴分词提取问题中的关键实体。
- `search_1` / `search_2` / `search_3`：分别处理不同类型的查询。
- `search_yangsheng`：处理复杂实体关系，支持自动构建查询语法。
- `/get_answer`：统一接口，实现问题分发与结果返回。

---

### 4.2 流程图

![外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传](https://img-home.csdnimg.cn/images/20230724024159.png?origin_url=query_flowchart.png&pos_id=img-8SNrFbY5-1767148698705)
（此处插入问答流程图：用户输入 → 分词 → 路由 → Cypher查询 → 结果返回 → LLM回退）

---

### 4.3 代码实例

```python
# 后端接口：获取问答结果
@app.route('/get_answer', methods=['POST'])
def get_answer():
    data = request.get_json()
    question = data.get('question', '')
  
    # 关键词提取
    targets = get_target_array(question)
  
    # 路由分发
    if '病' in question or '症候' in question:
        results = search_1(question, targets)
    elif '含' in question or '配伍' in question:
        results = search_3(question, targets)
    elif '治' in question or '药方' in question:
        results = search_2(question, targets)
    else:
        results = search_yangsheng(question, targets)
  
    # 若无结果，调用大模型生成
    if not results:
        # 调用外部 LLM API（如通义千问、Qwen）
        llm_response = call_llm(question)
        return jsonify({"result": llm_response})
  
    return jsonify({"result": results})
```

---

```python
# 数据库查询函数：药方组成查询
def search_3(query, targets):
    if not targets:
        return []
  
    node_name = targets[0]  # 举例：当归
    cypher = f"""
        MATCH (n:药材 {{name: '{node_name}'}})-[r:包含]-(m:药方)
        RETURN m.name, m.来源, m.主治症候
    """
    result = []
    with graph.session() as session:
        records = session.run(cypher)
        for record in records:
            result.append({
                'name': record['m.name'],
                '来源': record['m.来源'],
                '主治症候': record['m.主治症候']
            })
    return result
```

---

```sql
-- MySQL：用户表结构
CREATE TABLE tb_user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    nickname VARCHAR(50),
    roles ENUM('normal', 'admin') DEFAULT 'normal',
    avatar VARCHAR(200),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

```sql
-- MySQL：新闻表（含审核状态）
CREATE TABLE tb_news (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    content TEXT,
    author_id INT,
    status INT DEFAULT -1, -- -1: 未审核, 0: 已通过
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

```sql
-- Neo4j：药方与药材之间的关联
CREATE CONSTRAINT ON (n:药方) ASSERT n.name IS UNIQUE;
CREATE CONSTRAINT ON (n:药材) ASSERT n.name IS UNIQUE;
CREATE CONSTRAINT ON (n:典籍) ASSERT n.name IS UNIQUE;
```

---

> 文章结尾部分有CSDN官方提供的学长 联系方式名片
>
> 关注B站，私信获取！  [麦麦大数据](https://space.bilibili.com/1583208775)
编号: F067
