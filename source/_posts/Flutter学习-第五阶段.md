---
title: Flutter学习-第五阶段：2026 年的现代 Flutter 开发
date: 2026-08-08 21:30:00
tags:
  - Flutter
  - 学习笔记
categories: 学习笔记
---

> 这个系列的前四个阶段写于 2020 年（环境配置 → 路由管理 → Widget 基础 → ListView）。6 年过去，Flutter 已经从 1.x 迭代到 3.4x，很多东西都变了。这一阶段把「现在的 Flutter」重新梳理一遍。

我机器上的环境：Flutter 3.41.0-pre（master 通道），Dart 3.12。目前稳定版节奏是每年 4 个版本：3.41（2 月）、3.44（5 月）、3.47（8 月）、3.50（11 月）。

---

## 一、2026 年 Flutter 的几个大变化

### 1. Material / Cupertino 库正在解耦

官方正在把 Material 和 Cupertino 从 SDK 核心拆成独立 package。好处：

- 设计库不用等季度版 SDK，随时升级
- iOS 出 "Liquid Glass"、Android 出 "Material 3 Expressive" 这种大改版时，Flutter 能更快跟上
- 老项目可以锁 SDK 版本、只升设计包

### 2. Impeller 渲染引擎成为默认

当年iOS 上臭名昭著的「首次动画卡顿」（shader 编译 jank）已经成为历史——Impeller 在构建期预编译 shader，默认启用。

### 3. Widget Previews（实验性）

在 IDE 里直接预览单个 Widget，类似 Xcode 的 `#Preview`，还内置了 Flutter Inspector。做 UI 组件时不用跑整个 App 了。

### 4. 平台特定资源

`pubspec.yaml` 里可以指定资源只打进某些平台的包，移动端不用背桌面端的大资源：

```yaml
flutter:
  assets:
    - path: assets/logo.png
    - path: assets/web_worker.js
      platforms: [web]
```

### 5. iOS 插件正在迁往 Swift Package Manager

CocoaPods 逐渐退出 Flutter 生态，插件往 SwiftPM 迁移。iOS 开发者迁移插件时注意看插件的文档说明。

---

## 二、Dart 3 现代语法（2020 年那会还没有）

### Records（记录）—— 轻量多返回值

```dart
// 以前要写个类或者用 dynamic 列表，现在：
(double, String) getUser() => (18.5, '正常');

final (bmi, label) = getUser();
```

### Patterns + switch 表达式

```dart
String describe(int code) => switch (code) {
  200 => '成功',
  404 => '未找到',
  >= 500 => '服务器错误',
  _ => '未知',
};
```

### Sealed Class —— 状态管理的绝配

```dart
sealed class LoadState {}
class Loading extends LoadState {}
class Success extends LoadState { final data; Success(this.data); }
class Failure extends LoadState { final error; Failure(this.error); }

// switch 可以穷举检查，漏一个分支编译器直接报错
```

---

## 三、路由：从 Navigator 1.0 到 go_router

第二阶段的 `Navigator.push` 写法现在只推荐用于简单场景。正式项目用 **go_router**（声明式路由）：

```dart
final router = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (_, __) => HomePage()),
    GoRoute(
      path: '/detail/:id',               // 路径参数
      builder: (_, state) => DetailPage(id: state.pathParameters['id']!),
    ),
  ],
);

// 跳转
context.go('/detail/42');
context.push('/detail/42');
```

好处：深链接/Web URL 天然支持、路由表集中管理、嵌套导航（ShellRoute）做 TabBar 页面切换时各 Tab 独立路由栈。

---

## 四、状态管理：别再纠结了

2020 年大家的纠结：setState / Provider / Bloc / GetX / Redux… 2026 年的答案已经很收敛：

| 方案 | 适用 |
|------|------|
| **Riverpod 2** | 主流推荐。编译期安全、可测试、不依赖 BuildContext |
| Bloc | 大团队、强约束场景（事件→状态流转非常规范） |
| Provider | 老项目维护，新项目不建议再入坑 |
| setState | 单个页面内的局部状态，依然够用 |

Riverpod 的核心写法：

```dart
final counterProvider = StateProvider<int>((ref) => 0);

class CounterView extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Text('$count');
  }
}
```

---

## 五、动画新姿势

声明式的重复动画不用自己管 AnimationController 了：

```dart
RepeatingAnimationBuilder<Offset>(
  animatable: Tween<Offset>(begin: Offset(-1, 0), end: Offset(1, 0)),
  duration: Duration(seconds: 1),
  repeatMode: RepeatMode.reverse,
  builder: (context, offset, child) =>
      FractionalTranslation(translation: offset, child: child),
  child: FlutterLogo(size: 100),
)
```

---

## 六、这一阶段的学习建议

1. 老项目里的 `Navigator.push` 不用急着改，新页面用 go_router
2. 状态管理直接学 Riverpod，不要再从 Provider 入门
3. Dart 3 的 records/patterns/sealed 花半天过一遍，写代码立刻用得上
4. 关注 Material/Cupertino 解耦进度，明年设计库可能要单独引入

---

**系列回顾**：
- [第一阶段：环境配置](/2020/01/10/Flutter学习-第一阶段/)
- [第二阶段：路由管理](/2020/01/14/Flutter学习-第二阶段/)
- [第三阶段：Widget 基础](/2020/01/15/Flutter学习-第三阶段/)
- [第四阶段：ListView](/2020/01/16/Flutter学习-第四阶段/)
