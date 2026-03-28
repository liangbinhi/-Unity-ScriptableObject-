# Unity 物品合成系统

> 一个基于 Unity 与 ScriptableObject 实现的物品合成系统。  
> 支持拖拽物品、3x3 配方匹配、自动生成产物，以及与角色装备系统联动。

<img width="1920" height="872" alt="image" src="https://github.com/user-attachments/assets/7419ee96-7b69-4c62-b94f-9a37ba6f0bf2" />

---

## 项目简介

这是一个常见于 RPG、生存、沙盒类游戏中的物品合成系统。

玩家可以将背包中的材料拖入 3x3 合成格中，当当前摆放方式与某个配方匹配时，系统会自动生成对应物品。

与直接在代码里写死配方不同，本项目使用 ScriptableObject 配置物品与配方，使系统更容易扩展和维护。

---

## 演示效果

### 合成界面

<img width="589" height="356" alt="image" src="https://github.com/user-attachments/assets/f0d5d22e-4b92-4912-bc8b-5f760d708ab4" />


### 拖拽过程

<img width="1473" height="872" alt="image" src="https://github.com/user-attachments/assets/5e76cb74-4f00-4246-a3c5-5e806d2cf062" />


### 装备联动

<img width="1464" height="814" alt="image" src="https://github.com/user-attachments/assets/4a675aaf-f3cf-49d0-a63f-bd70010fca9d" />


---

## 功能特点

- 3x3 合成网格
- 拖拽物品交互
- 基于 ScriptableObject 的物品与配方配置
- 自动匹配配方并生成产物
- 支持物品堆叠
- 支持物品拆分
- 支持与角色装备系统联动
- UI 与逻辑分离，方便后续扩展

---

## 系统流程

```text
玩家背包
   ↓
拖动物品到合成格
   ↓
CraftingSystem 检测配方
   ↓
生成目标物品
   ↓
放入背包或直接装备
```

<img width="390" height="459" alt="image" src="https://github.com/user-attachments/assets/508bea8a-9d8f-485e-9f58-52deb777476b" />

---

## 核心脚本说明

### CraftingSystem.cs

负责：

- 管理 3x3 合成格
- 判断当前摆放是否匹配某个配方
- 生成合成结果
- 在取走产物后自动消耗材料

系统内部使用二维数组保存合成格中的物品：

```csharp
private Item[,] itemArray;
```

每次向格子中放入物品后，都会重新检测当前是否存在匹配的配方。

---

### RecipeScriptableObject.cs

用于通过 ScriptableObject 配置合成配方。

每个配方包含：

- 最终输出物品
- 3x3 网格中每个位置需要的材料

```csharp
public ItemScriptableObject output;

public ItemScriptableObject item_00;
public ItemScriptableObject item_10;
public ItemScriptableObject item_20;
```

这样就可以直接在 Unity Inspector 中新增配方，而不需要修改代码。

---

### ItemScriptableObject.cs

用于保存物品数据，例如：

- 物品类型
- 名称
- 图标
- 对应装备槽位

```csharp
public Item.ItemType itemType;
public string itemName;
public Sprite itemSprite;
public CharacterEquipment.EquipSlot equipSlot;
```

整个物品系统的数据都由 ScriptableObject 驱动。

---

### UI_CraftingSystem.cs

负责刷新合成界面与输出栏。

当合成系统中的内容发生变化时，会自动刷新 UI：

```csharp
craftingSystem.OnGridChanged += CraftingSystem_OnGridChanged;
```

UI 与逻辑解耦，方便后续增加动画、提示或新的界面。

---

### UI_Item.cs 与 UI_ItemDrag.cs

负责：

- 拖动物品
- 显示被拖动的图标
- 显示堆叠数量
- 支持拆分物品

```csharp
public void OnBeginDrag(PointerEventData eventData)
```

对于可堆叠物品，右键可以将数量拆分为两部分。

---

### CharacterEquipment.cs

合成出的武器、防具可以直接装备到角色身上。

系统支持：

- 武器槽
- 护甲槽
- 头盔槽

```csharp
public enum EquipSlot {
    None,
    Helmet,
    Armor,
    Weapon
}
```

装备变化后，会同步刷新角色外观与装备栏 UI。

---

## 项目结构

```text
Assets
├── Scripts
│   ├── Crafting
│   │   ├── CraftingSystem.cs
│   │   ├── RecipeScriptableObject.cs
│   │   ├── ItemScriptableObject.cs
│   │   └── IItemHolder.cs
│   ├── Character
│   │   ├── CharacterEquipment.cs
│   │   └── Player.cs
│   ├── UI
│   │   ├── UI_CraftingSystem.cs
│   │   ├── UI_CraftingItemSlot.cs
│   │   ├── UI_Item.cs
│   │   ├── UI_ItemDrag.cs
│   │   ├── UI_CharacterEquipment.cs
│   │   └── UI_CharacterEquipmentSlot.cs
│   └── Test
│       └── CraftingSystem_Testing.cs
```

---

## 示例配方

```text
钻石
钻石
木棍
↓
钻石剑
```



---

## 后续可扩展方向

- 支持镜像配方与偏移配方
- 增加合成动画、音效与提示
- 使用 JSON / SQLite 保存合成与背包数据
- 支持批量合成
- 增加配方解锁系统
- 与更完整的背包系统整合
- 增加不同工作台、等级限制或制作条件

---

## 使用方式

1. 创建 `ItemScriptableObject`
2. 创建 `RecipeScriptableObject`
3. 在 `CraftingSystem_Testing` 中配置配方列表
4. 运行场景
5. 将物品拖入合成格中测试

---

## 我在这个项目中主要练习了什么

通过这个项目，我重点练习了：

- 使用 ScriptableObject 做数据驱动设计
- 设计可扩展的背包与合成系统
- 实现 UI 拖拽与物品交互
- 将“背包 → 合成 → 装备”多个模块串联起来
- 将逻辑层与 UI 层分离，提升项目可维护性

这个项目不仅实现了基础功能，也为后续扩展成更完整的 RPG 装备制作系统打下了基础。
