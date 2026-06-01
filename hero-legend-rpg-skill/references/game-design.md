# 勇者传说 RPG - 游戏数据和设计说明

> 本文档可作为 Skill 供其他开发者参考和复用

**开发者**：张晨 by TRAE AI  
**版本**：v1.1.0

---

## 📋 目录

1. [游戏架构](#游戏架构)
2. [数据配置](#数据配置)
3. [核心系统设计](#核心系统设计)
4. [数值平衡](#数值平衡)
5. [UI/UX设计](#uiux设计)
6. [音乐系统](#音乐系统)
7. [扩展指南](#扩展指南)

---

## 游戏架构

### 技术栈
- **HTML5**：页面结构
- **CSS3**：样式和动画
- **JavaScript (ES6)**：游戏逻辑
- **localStorage**：数据持久化

### 文件结构
```
勇者传说RPG.html
├── <style>          // 所有CSS样式
├── <body>           // HTML结构
│   ├── 游戏容器
│   ├── 地图进场CG
│   ├── BOSS结算CG
│   ├── 失败结算CG
│   ├── 左侧面板（角色信息、装备）
│   ├── 中间地图区域
│   ├── 右侧日志
│   └── 各种弹窗（战斗、背包、技能等）
└── <script>         // 游戏逻辑
    ├── 配置常量（QUALITIES, WEAPONS, MONSTERS, BOSSES, MAPS, SKILLS, MUSIC_CONFIG）
    ├── 游戏状态对象（game）
    └── 战斗系统对象（battle）
```

### 核心对象
```javascript
const game = {
    player: { /* 玩家数据 */ },
    currentMap: { /* 当前地图数据 */ },
    currentPath: null,  // 移动路径
    healInterval: null,  // 村庄恢复定时器
    // 方法...
}

const battle = {
    enemy: null,
    speed: 1,
    isBoss: false,
    enemySkillCooldown: 0,  // BOSS技能冷却
    enemyAtkBoostTurns: 0,  // BOSS攻击增益回合
    enemyDefBoostTurns: 0,  // BOSS防御增益回合
    // 方法...
}
```

---

## 数据配置

### 1. 品质系统 (QUALITIES)

```javascript
const QUALITIES = [
    { name: '普通', class: 'quality-common', multiplier: 1.0, color: '#95a5a6' },
    { name: '精良', class: 'quality-uncommon', multiplier: 1.5, color: '#2ecc71' },
    { name: '优秀', class: 'quality-rare', multiplier: 2.2, color: '#3498db' },
    { name: '史诗', class: 'quality-epic', multiplier: 3.5, color: '#9b59b6' },
    { name: '传说', class: 'quality-legendary', multiplier: 5.0, color: '#f39c12' }
];
```

**设计说明**：
- 品质影响装备属性倍率（1.0x - 5.0x）
- 高品质装备即使等级低也优于低品质高等级装备
- 颜色用于UI显示区分

### 2. 武器配置 (WEAPONS)

```javascript
const WEAPONS = [
    { id: 'sword', name: '铁剑', emoji: '🗡️', avatar: '🗡️', hpMod: 1.0, atkMod: 1.0, defMod: 1.0 },
    { id: 'axe', name: '战斧', emoji: '🪓', avatar: '🪓', hpMod: 0.8, atkMod: 1.3, defMod: 0.7 },
    { id: 'staff', name: '法杖', emoji: '🔮', avatar: '🔮', hpMod: 1.2, atkMod: 0.9, defMod: 0.9 },
    { id: 'dagger', name: '匕首', emoji: '🗡️', avatar: '🗡️', hpMod: 0.6, atkMod: 1.1, defMod: 1.1 },
    { id: 'greatsword', name: '巨剑', emoji: '⚔️', avatar: '⚔️', hpMod: 1.1, atkMod: 1.2, defMod: 0.6 }
];
```

**设计说明**：
- `hpMod/atkMod/defMod`：属性修正系数
- 不同武器有不同的属性倾向

### 3. 怪物配置 (MONSTERS)

```javascript
const MONSTERS = [
    { id: 'slime', name: '史莱姆', emoji: '💧', minLv: 1, maxLv: 3, 
      hpBase: 15, atkBase: 0.6, defBase: 0.4, size: 1 },
    // ... 29种怪物
];
```

**属性计算公式**：
```javascript
hp = Math.floor(level * hpBase * (0.8 + Math.random() * 0.4) * qualityMult);
atk = Math.floor(level * atkBase * (0.8 + Math.random() * 0.4) * qualityMult * 1.3);
def = Math.floor(level * defBase * (0.8 + Math.random() * 0.4) * qualityMult);
```

### 4. BOSS配置 (BOSSES)

```javascript
const BOSSES = [
    { name: '哥布林王', emoji: '👺', level: 8, hpMult: 1.8, atkMult: 1.8, defMult: 1.8,
      skill: { name: '重击', emoji: '⚔️', desc: '1.5倍伤害', cooldown: 3, multiplier: 1.5 } },
    // ... 9个BOSS，每个都有专属技能
    { name: '创世神', emoji: '👑', level: 80, hpMult: 4.2, atkMult: 2.0, defMult: 2.0,
      skill: { name: '创世之光', emoji: '🌟', desc: '6倍伤害+恢复20%', cooldown: 15, multiplier: 6, healPercent: 0.2 } }
];
```

**BOSS属性计算**：
```javascript
hp = Math.floor(bossData.level * 55 * bossData.hpMult);
atk = Math.floor(bossData.level * 1.4 * bossData.atkMult);
def = Math.floor(bossData.level * 0.8 * bossData.defMult);
exp = bossData.level * 8;  // 8倍经验
```

**BOSS技能AI**：
- 冷却好后50%概率使用技能
- 技能类型：伤害倍率、连击、防御增益、攻击增益、恢复
- 技能冷却和玩家解锁的技能一致

### 5. 地图配置 (MAPS)

```javascript
const MAPS = [
    { id: 0, name: '新手村外围', emoji: '🌿', minLv: 1, maxLv: 5, 
      rows: 12, cols: 22, obstacleRate: 0.12, monsterCount: 18, 
      monsters: ['slime', 'mushroom', 'bat'], 
      bgClass: 'map-bg-village', 
      obstacles: { types: ['🌳', '🌲', '🌿'] }, 
      emptyEmoji: '🟫', 
      desc: '宁静的村庄外围，适合新手冒险者练级' },
    // ... 9层地图
];
```

**地图等级分布**：
| 层 | 名称 | 等级 | BOSS等级 |
|---|------|------|---------|
| 1 | 新手村外围 | 1-5 | 8 |
| 2 | 迷雾森林 | 7-15 | 18 |
| 3 | 废弃矿坑 | 17-25 | 28 |
| 4 | 熔岩地带 | 25-35 | 38 |
| 5 | 冰封雪原 | 35-47 | 52 |
| 6 | 龙之巢穴 | 45-56 | 60 |
| 7 | 天空之城 | 50-58 | 68 |
| 8 | 无尽深渊 | 62-69 | 75 |
| 9 | 创世神域 | 70-75 | 80 |

### 6. 技能配置 (SKILLS)

```javascript
const SKILLS = [
    { level: 10, name: '重击', emoji: '⚔️', desc: '1.5倍伤害', cooldown: 3, multiplier: 1.5 },
    { level: 20, name: '防御', emoji: '🛡️', desc: '防御翻倍', cooldown: 5, defenseBoost: 2 },
    { level: 30, name: '连击', emoji: '⚡', desc: '攻击2次', cooldown: 4, hits: 2 },
    { level: 40, name: '狂暴', emoji: '🔥', desc: '攻击+30%', cooldown: 6, duration: 3, atkBoost: 0.3 },
    { level: 50, name: '圣光', emoji: '✨', desc: '恢复30%', cooldown: 8, healPercent: 0.3 },
    { level: 60, name: '终极', emoji: '💥', desc: '3倍伤害', cooldown: 10, multiplier: 3 },
    { level: 70, name: '神罚', emoji: '⚡', desc: '4倍伤害', cooldown: 12, multiplier: 4 },
    { level: 80, name: '创世', emoji: '🌟', desc: '5倍伤害', cooldown: 15, multiplier: 5 }
];
```

---

## 核心系统设计

### 1. 战斗系统

**伤害计算**：
```javascript
calculateDamage(atk, def) {
    const floatAtk = Math.floor(atk * (0.75 + Math.random() * 0.5));
    if (floatAtk > def) return floatAtk - def;
    // 最低伤害根据地图层数递增
    return Math.floor(Math.random() * maxMinDamage) + 1;
}
```

**战斗流程**：
1. 玩家攻击/使用技能 → 计算伤害 → 扣除敌人HP → 播放技能动画 → 显示伤害数字
2. 敌人HP > 0 → 敌人反击/使用技能 → 计算伤害 → 扣除玩家HP
3. 循环直到一方HP ≤ 0

**BOSS技能AI**：
1. 检查技能冷却是否就绪
2. 50%概率决定是否使用技能
3. 使用后进入冷却
4. 支持多种技能类型：伤害倍率、连击、防御/攻击增益、恢复

### 2. 装备生成系统

**掉落逻辑**：
```javascript
generateDrop(monsterLevel, isBoss = false, traitBonus = 0) {
    // 1. 随机武器/防具
    // 2. 计算等级（怪物等级±3）
    // 3. 随机品质（BOSS最低史诗）
    // 4. 计算基础属性 × 品质倍率(1.0-5.0)
    // 5. 应用浮动范围（±25%）
}
```

**品质概率**：
| 品质 | 概率 |
|------|------|
| 普通 | 35% |
| 精良 | 30% |
| 优秀 | 20% |
| 史诗 | 10% |
| 传说 | 5% |

### 3. 怪物品质系统

```javascript
createMonster(template, level) {
    const rand = Math.random();
    if (rand < 0.70) {
        // 普通 70%: 1层血, expMult 1.0
    } else if (rand < 0.95) {
        // 精英 25%: 2层血, expMult 2.0
    } else {
        // 稀有 5%: 2层血, expMult 5.0
    }
}
```

**怪物阶段系统**：
- 血量 < 50%：防御提升
- 血量 < 30%：攻击提升
- 精英/稀有提升幅度更大

### 4. 升级系统

**经验值公式**：
```javascript
expNeeded = 50 + (level - 1) * 50;  // 每级相差50
```

**升级奖励**：
- HP +20, ATK +1, DEF +1
- 满血恢复

### 5. 移动系统

**路径查找**：BFS算法寻路，避开障碍物和怪物

**移动指引**：
- 移动前显示路径（金色高亮）
- 到达后清除路径
- 传送门需步行到达后才能使用

### 6. 村庄恢复

**恢复机制**：
- 进入村庄触发逐渐恢复（10步回满）
- 离开村庄立即停止恢复
- 防止重复创建定时器

---

## 数值平衡

### 玩家成长曲线

| 等级 | HP | ATK | DEF | 所需经验 |
|------|-----|-----|-----|----------|
| 1 | 50 | 5 | 5 | 50 |
| 10 | 230 | 14 | 14 | 500 |
| 30 | 630 | 34 | 34 | 1500 |
| 50 | 1030 | 54 | 54 | 2500 |
| 80 | 1630 | 84 | 84 | 4000 |

### 怪物经验值

| 类型 | 倍率 | 公式 |
|------|------|------|
| 普通怪 | 1.0x | level × 10 × 1.0 |
| 精英怪 | 2.0x | level × 10 × 2.0 |
| 稀有怪 | 5.0x | level × 10 × 5.0 |
| BOSS | 8.0x | level × 8 |

### 装备品质倍率

| 品质 | 倍率 | +5传说 vs +11普通 |
|------|------|-------------------|
| 普通 | 1.0x | 11 × 1.0 = 11 |
| 精良 | 1.5x | - |
| 优秀 | 2.2x | - |
| 史诗 | 3.5x | - |
| 传说 | 5.0x | 5 × 5.0 = 25 ✅ |

---

## UI/UX设计

### 配色方案

**主题**：日式西幻夕阳风格

| 元素 | 颜色 | 用途 |
|------|------|------|
| 背景 | 深褐→橙黄渐变 | 夕阳氛围 |
| 标题 | 金橙色渐变 | 史诗感 |
| 血量(≥50%) | 蓝色 | 健康 |
| 血量(30-50%) | 黄色 | 警告 |
| 血量(<30%) | 红色 | 危险 |
| 经验 | 靛蓝→紫罗兰 | 魔法能量 |
| 边框 | 金橙色微光 | 金属质感 |

### 动画效果

1. **伤害数字弹出**：弹出 → 放大 → 上浮消失（1秒）
2. **升级特效**：全屏金色闪光 + 文字放大动画
3. **BOSS边框**：红色脉冲发光动画
4. **地图进场CG**：3秒淡入淡出，显示地图名称/等级/描述
5. **移动路径**：金色高亮显示路径格子
6. **技能专属动画**（1.5-2秒）：
   - 重击：金光冲刺
   - 防御：蓝色护盾
   - 连击：快速左右冲刺
   - 狂暴：红色燃烧
   - 恢复：绿色治愈光
   - 终极：金色大爆发
   - BOSS版本：对应红色/紫色变体
7. **技能名称弹出**：金色(玩家)/红色(BOSS)文字上浮消失

---

## 音乐系统

### 多源加载

```javascript
sources: [
    { base: 'music/', name: '本地' },
    { base: 'https://raw.githubusercontent.com/.../', name: 'GitHub' },
    { base: 'https://cdn.jsdelivr.net/gh/.../', name: 'jsDelivr' }
]
```

### BGM配置

**3套随机BGM**（每次进入新地图随机选一套）：
```
套装1: map_bgm.mp3 / battle_normal.mp3 / battle_boss.mp3
套装2: map_bgm2.mp3 / battle_normal2.mp3 / battle_boss2.mp3
套装3: map_bgm3.mp3 / battle_normal3.mp3 / battle_boss3.mp3
```

**BOSS专属BGM**（5-9层）：
```
5层: god_battle.mp3
6层: god_battle2.mp3
7层: god_battle3.mp3
8层: god_battle4.mp3
9层: god_battle5.mp3
```

**固定BGM**：
```
victory.mp3        - 通关胜利
defeat_normal.mp3  - 普通战斗死亡
defeat_boss.mp3    - BOSS战斗死亡
```

---

## 扩展指南

### 添加新地图

1. 在 `MAPS` 数组添加配置
2. 在 CSS 添加背景样式 `.map-bg-xxx`
3. 在 `BOSSES` 添加对应BOSS（含专属技能）
4. 如需专属BOSS BGM，在 `bossBGMs` 数组添加

### 添加新怪物

1. 在 `MONSTERS` 数组添加配置
2. 确保emoji不重复
3. 在地图配置的 `monsters` 数组中引用

### 添加新技能

1. 在 `SKILLS` 数组添加配置
2. 在 `BOSSES` 对应BOSS添加相同技能
3. 实现技能效果逻辑
4. 添加对应的CSS动画类

### 添加新装备类型

1. 在 `WEAPONS` 或 `ARMORS` 添加配置
2. 设计属性修正系数
3. 选择合适的emoji图标

---

## 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.0.0 | 2025-05 | 初始版本发布 |
| v1.1.0 | 2025-06 | BOSS专属技能AI、技能动画、装备品质倍率调整、地图进场CG、移动路径指引、3套随机BGM+神战BGM、地图等级优化、死亡BGM |

---

## 许可证

MIT License - 可自由使用、修改和分发

---

> 📝 本文档由 张晨 by TRAE AI 生成，供游戏开发者参考学习
