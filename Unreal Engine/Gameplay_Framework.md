# Unreal Gameplay Framework Guide for C++

Unreal 4 的 Gameplay 框架为构建游戏提供了强大的类。理解这个框架非常重要，它会贯穿整个 Unreal 引擎的学习。

- Object: base class for all unreal objects
- Actor: objects that can be placed or spawned in the world.
- Pawn: actors that can be ‘controlled’ by players or AI.
- Character: pawn that implements walking movement


## Actor

- Actor 是关卡中任何游戏对象的基类
- Actor 可以通过调用 `SetReplicates(true)` 复制(包括多人游戏)。
- Actor 支持接收伤害

## Pawn

物理或视觉上的可以控制的 player 或 AI

## UObject


