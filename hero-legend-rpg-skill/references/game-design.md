# 勇者传说 RPG - 游戏数据和设计说明

> 本文档可作为 Skill 供其他开发者参考和复用

**开发者**：张晨 by TRAE AI

---

## 📋 目录

1. [游戏架构](#游戏架构)
2. [数据配置](#数据配置)
3. [核心系统设计](#核心系统设计)
4. [数值平衡](#数值平衡)
5. [UI/UX设计](#uiux设计)
6. [扩展指南](#扩展指南)

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
│   ├── 左侧面板（角色信息、装备）
│   ├── 中间地图区域
│   ├── 右侧日志
│   └── 各种弹窗（战斗、背包、技能等）
└── <script>         // 游戏逻辑
    ├── 配置常量（QUALITIES, WEAPONS, MONSTERS, BOSSES, MAPS, SKILLS）
    ├── 游戏状态对象（game）
    └── 战斗系统对象（battle）
```

### 核心对象
```javascript
const game = {
    player: { /* 玩家数据 */ },
    currentMap: { /* 当前地图数据 */ },
    // 方法...
}

const battle = {
    enemy: null,
    speed: 1,
    // 方法...
}
```

---

## 数据配置

### 1. 品质系统 (QUALITIES)

```javascript
const QUALITIES = [
    { name: '普通', class: 'quality-common', multiplier: 1.0, color: '#95a5a6' },
    { name: '精良', class: 'quality-uncommon', multiplier: 1.2, color: '#2ecc71' },
    { name: '优秀', class: 'quality-rare', multiplier: 1.5, color: '#3498db' },
    { name: '史诗', class: 'quality-epic', multiplier: 2.0, color: '#9b59b6' },
    { name: '传说', class: 'quality-legendary', multiplier: 3.0, color: '#f39c12' }
];
```

**设计说明**：
- 品质影响装备属性倍率
- 颜色用于UI显示区分
- multiplier 范围：1.0 - 3.0

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
    // ... 更多怪物
];
```

**属性计算公式**：
```javascript
hp = Math.floor(level * hpBase * (0.8 + Math.random() * 0.4) * qualityMult);
atk = Math.floor(level * atkBase * (0.8 + Math.random() * 0.4) * qualityMult * 1.3); // 1.3为攻击加成
def = Math.floor(level * defBase * (0.8 + Math.random() * 0.4) * qualityMult);
```

### 4. BOSS配置 (BOSSES)

```javascript
const BOSSES = [
    { name: '哥布林王', emoji: '👺', level: 6, hpMult: 1.8, atkMult: 1.8, defMult: 1.8 },
    // ... 更多BOSS
    { name: '创世神', emoji: '👑', level: 80, hpMult: 2.0, atkMult: 2.0, defMult: 2.0 } // 最终BOSS加强
];
```

**BOSS属性计算**：
```javascript
hp = Math.floor(bossData.level * 55 * bossData.hpMult);
atk = Math.floor(bossData.level * 1.4 * bossData.atkMult);
def = Math.floor(bossData.level * 0.8 * bossData.defMult);
exp = bossData.level * 50;
```

### 5. 地图配置 (MAPS)

```javascript
const MAPS = [
    { id: 0, name: '🌿 新手村外围', emoji: '🌿', minLv: 1, maxLv: 5, 
      rows: 12, cols: 20, obstacleRate: 0.12, monsterCount: 18, 
      monsters: ['slime', 'mushroom', 'bat'], 
      bgClass: 'map-bg-village', 
      obstacles: { types: ['🌳', '🌲', '🌿'] }, 
      emptyEmoji: '🟫' },
    // ... 更多地图
];
```

**地图参数说明**：
| 参数 | 说明 |
|------|------|
| rows/cols | 地图尺寸（格子数） |
| obstacleRate | 障碍物密度 |
| monsterCount | 怪物数量 |
| monsters | 可出现的怪物ID列表 |
| bgClass | 背景样式类名 |

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
    // 攻击浮动：75%-125%
    const floatAtk = Math.floor(atk * (0.75 + Math.random() * 0.5));
    if (floatAtk > def) return floatAtk - def;
    // 最低伤害：1-4
    return Math.floor(Math.random() * 4) + 1;
}
```

**战斗流程**：
1. 玩家攻击 → 计算伤害 → 扣除敌人HP → 显示伤害数字
2. 敌人HP > 0 → 敌人反击 → 计算伤害 → 扣除玩家HP
3. 循环直到一方HP ≤ 0

### 2. 装备生成系统

**掉落逻辑**：
```javascript
generateDrop(monsterLevel, isBoss = false, traitBonus = 0) {
    // 1. 随机武器/防具
    // 2. 计算等级（怪物等级±3）
    // 3. 随机品质（BOSS最低优秀）
    // 4. 计算基础属性
    // 5. 应用品质倍率
    // 6. 应用浮动范围（±25%）
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
        // 普通 70%
        quality = 'normal'; hpMult = 1.0; atkMult = 1.0; expMult = 1.0;
    } else if (rand < 0.95) {
        // 精英 25%
        quality = 'elite'; hpMult = 1.5; atkMult = 1.3; expMult = 1.5;
    } else {
        // 稀有 5%
        quality = 'rare'; hpMult = 2.0; atkMult = 1.6; expMult = 2.0;
    }
}
```

### 4. 升级系统

**经验值公式**：
```javascript
expNeeded = 10 + (level - 1) * 20;  // 每级相差20
```

**升级奖励**：
- HP +20
- ATK +1
- DEF +1
- 满血恢复

---

## 数值平衡

### 玩家成长曲线

| 等级 | HP | ATK | DEF | 所需经验 |
|------|-----|-----|-----|----------|
| 1 | 50 | 5 | 5 | 10 |
| 10 | 230 | 14 | 14 | 190 |
| 30 | 630 | 34 | 34 | 590 |
| 50 | 1030 | 54 | 54 | 990 |
| 80 | 1630 | 84 | 84 | 1590 |

### 怪物难度曲线

**设计原则**：
- 同等级怪物 ≈ 玩家属性的 0.8-1.2 倍
- BOSS属性 = 普通怪物 × 1.8
- 最终BOSS = 普通怪物 × 2.0

### 装备价值评估

**一件优秀品质武器的价值**：
```
基础属性 × 1.5（品质）× 0.75-1.25（浮动）
```

---

## UI/UX设计

### 配色方案

**主题**：日式西幻夕阳风格

| 元素 | 颜色 | 用途 |
|------|------|------|
| 背景 | 深褐→橙黄渐变 | 夕阳氛围 |
| 标题 | 金橙色渐变 | 史诗感 |
| 血量 | 深红→鲜红→橙红 | 夕阳血色 |
| 经验 | 靛蓝→紫罗兰 | 魔法能量 |
| 边框 | 金橙色微光 | 金属质感 |

### 动画效果

1. **伤害数字弹出**：
   - 位置：被攻击方头顶
   - 动画：弹出 → 放大 → 上浮消失
   - 时长：1秒

2. **升级特效**：
   - 全屏金色闪光
   - 文字放大动画

3. **BOSS边框**：
   - 红色脉冲发光动画

---

## 扩展指南

### 添加新地图

1. 在 `MAPS` 数组添加配置
2. 在 CSS 添加背景样式 `.map-bg-xxx`
3. 在 `BOSSES` 添加对应BOSS

### 添加新怪物

1. 在 `MONSTERS` 数组添加配置
2. 确保emoji不重复
3. 在地图配置的 `monsters` 数组中引用

### 添加新技能

1. 在 `SKILLS` 数组添加配置
2. 实现技能效果逻辑
3. 更新战斗系统处理新效果类型

### 添加新装备类型

1. 在 `WEAPONS` 或 `ARMORS` 添加配置
2. 设计属性修正系数
3. 选择合适的emoji图标

---

## 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.0.0 | 2025-05 | 初始版本发布 |

---

## 许可证

MIT License - 可自由使用、修改和分发

---

> 📝 本文档由 张晨 by TRAE AI 生成，供游戏开发者参考学习
