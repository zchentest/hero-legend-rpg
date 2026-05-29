# 数据配置模板

本文档提供可直接复用的数据配置模板。

---

## 品质系统模板

```javascript
const QUALITIES = [
    { name: '普通', class: 'quality-common', multiplier: 1.0, color: '#95a5a6' },
    { name: '精良', class: 'quality-uncommon', multiplier: 1.2, color: '#2ecc71' },
    { name: '优秀', class: 'quality-rare', multiplier: 1.5, color: '#3498db' },
    { name: '史诗', class: 'quality-epic', multiplier: 2.0, color: '#9b59b6' },
    { name: '传说', class: 'quality-legendary', multiplier: 3.0, color: '#f39c12' }
];
```

---

## 武器模板

```javascript
const WEAPONS = [
    { id: 'sword', name: '铁剑', emoji: '🗡️', avatar: '🗡️', hpMod: 1.0, atkMod: 1.0, defMod: 1.0 },
    { id: 'axe', name: '战斧', emoji: '🪓', avatar: '🪓', hpMod: 0.8, atkMod: 1.3, defMod: 0.7 },
    { id: 'staff', name: '法杖', emoji: '🔮', avatar: '🔮', hpMod: 1.2, atkMod: 0.9, defMod: 0.9 },
    { id: 'dagger', name: '匕首', emoji: '🗡️', avatar: '🗡️', hpMod: 0.6, atkMod: 1.1, defMod: 1.1 },
    { id: 'greatsword', name: '巨剑', emoji: '⚔️', avatar: '⚔️', hpMod: 1.1, atkMod: 1.2, defMod: 0.6 }
];
```

---

## 防具模板

```javascript
const ARMORS = [
    { id: 'leather', name: '皮甲', emoji: '🦺', avatar: '🦺', hpMod: 1.0, atkMod: 1.0, defMod: 1.0 },
    { id: 'plate', name: '铁甲', emoji: '🛡️', avatar: '🛡️', hpMod: 1.2, atkMod: 0.6, defMod: 1.3 },
    { id: 'robe', name: '法袍', emoji: '👘', avatar: '👘', hpMod: 1.4, atkMod: 0.9, defMod: 0.7 },
    { id: 'chain', name: '链甲', emoji: '🦺', avatar: '🦺', hpMod: 1.1, atkMod: 0.8, defMod: 1.1 },
    { id: 'dragon', name: '龙鳞甲', emoji: '🐲', avatar: '🐲', hpMod: 1.3, atkMod: 1.0, defMod: 1.2 }
];
```

---

## 怪物模板

```javascript
const MONSTERS = [
    // 格式: { id, name, emoji, minLv, maxLv, hpBase, atkBase, defBase, size }
    { id: 'slime', name: '史莱姆', emoji: '💧', minLv: 1, maxLv: 3, hpBase: 15, atkBase: 0.6, defBase: 0.4, size: 1 },
    { id: 'goblin', name: '哥布林', emoji: '👹', minLv: 4, maxLv: 7, hpBase: 20, atkBase: 0.8, defBase: 0.5, size: 1 },
    { id: 'skeleton', name: '骷髅兵', emoji: '💀', minLv: 8, maxLv: 12, hpBase: 24, atkBase: 0.8, defBase: 0.6, size: 1 },
    { id: 'demon', name: '恶魔', emoji: '👿', minLv: 22, maxLv: 28, hpBase: 32, atkBase: 1.3, defBase: 0.5, size: 1 },
    { id: 'dragon', name: '巨龙', emoji: '🐉', minLv: 28, maxLv: 35, hpBase: 55, atkBase: 1.4, defBase: 0.8, size: 1 }
];
```

---

## BOSS模板

```javascript
const BOSSES = [
    // 格式: { name, emoji, level, hpMult, atkMult, defMult }
    { name: '哥布林王', emoji: '👺', level: 6, hpMult: 1.8, atkMult: 1.8, defMult: 1.8 },
    { name: '骷髅领主', emoji: '💀', level: 22, hpMult: 1.8, atkMult: 1.8, defMult: 1.8 },
    { name: '炎魔', emoji: '🔥', level: 38, hpMult: 1.8, atkMult: 1.8, defMult: 1.8 },
    { name: '龙神', emoji: '🐉', level: 60, hpMult: 1.8, atkMult: 1.8, defMult: 1.8 },
    { name: '创世神', emoji: '👑', level: 80, hpMult: 2.0, atkMult: 2.0, defMult: 2.0 }
];
```

---

## 地图模板

```javascript
const MAPS = [
    // 格式: { id, name, emoji, minLv, maxLv, rows, cols, obstacleRate, monsterCount, monsters, bgClass, obstacles, emptyEmoji }
    { 
        id: 0, 
        name: '🌿 新手村外围', 
        emoji: '🌿', 
        minLv: 1, 
        maxLv: 5, 
        rows: 12, 
        cols: 20, 
        obstacleRate: 0.12, 
        monsterCount: 18, 
        monsters: ['slime', 'goblin'], 
        bgClass: 'map-bg-village', 
        obstacles: { types: ['🌳', '🌲', '🌿'] }, 
        emptyEmoji: '🟫' 
    }
];
```

---

## 技能模板

```javascript
const SKILLS = [
    // 格式: { level, name, emoji, desc, cooldown, [multiplier|hits|healPercent|...] }
    { level: 10, name: '重击', emoji: '⚔️', desc: '1.5倍伤害', cooldown: 3, multiplier: 1.5 },
    { level: 20, name: '防御', emoji: '🛡️', desc: '防御翻倍', cooldown: 5, defenseBoost: 2 },
    { level: 30, name: '连击', emoji: '⚡', desc: '攻击2次', cooldown: 4, hits: 2 },
    { level: 40, name: '狂暴', emoji: '🔥', desc: '攻击+30%', cooldown: 6, duration: 3, atkBoost: 0.3 },
    { level: 50, name: '圣光', emoji: '✨', desc: '恢复30%', cooldown: 8, healPercent: 0.3 },
    { level: 60, name: '终极', emoji: '💥', desc: '3倍伤害', cooldown: 10, multiplier: 3 }
];
```

---

## 角色形象模板

```javascript
const CHARACTERS = [
    { emoji: '🧙‍♂️', name: '法师' },
    { emoji: '🧝‍♂️', name: '战士' },
    { emoji: '🤴', name: '骑士' },
    { emoji: '👸', name: '公主' }
];
```

---

## 使用说明

1. 复制所需模板到你的项目中
2. 根据需要修改参数值
3. 确保emoji不重复
4. 调整数值平衡以适应你的游戏难度

---

> 📝 模板基于《勇者传说RPG》实际配置
