# sakura-exhibit

极简风格樱花动画网页，基于 Canvas + SVG 实现。

## 页面

| 文件 | 效果 |
|------|------|
| [index.html](index.html) | 扇形樱花树，博物馆展陈风格 |
| [heart.html](heart.html) | 爱心形樱花树 |
| [fractal.html](fractal.html) | 分形递归生长树，蓝色背景 |
| [upward.html](upward.html) | 仰视视角繁花，配中文诗句 |

在线预览：**https://wemamawe.github.io/sakura-exhibit/**

---

## 技术实现

### 博物馆系列（index.html / heart.html）

- 纯 **Canvas 2D** 绘制，逐帧动画
- 枝条按预设坐标数组依次绘制，模拟生长效果
- 花朵使用 5 瓣椭圆 + easeBack 弹出缩放
- 围栏立柱 translateY 入场，绳子逐段描边
- 花瓣粒子循环飘落

### 分形生长系列（fractal.html）

- **递归二叉树**（L-System 思路），随机扰动角度和长度
- 蒲公英放射状节点：各分叉点绘制射线 + 端点圆
- Canvas 渐变天空背景
- 枝条按生成顺序 ID 控制出现时序

### 仰视系列（upward.html）

- 三根主干从画面顶部向下延伸，模拟仰视视角
- 递归分支 + 花朵/叶片按 branch ID 延迟显示
- 中文文字淡入，粒子飘落

---

## 动画时序（博物馆系列）

```
0s        主干开始生长
~1s       主枝延伸
~1.5s     花朵分批绽放（easeBack 弹出）
~2.5s     围栏立柱升起
~3s       绳子从左到右展开
~3.5s     文字淡入
~4s+      花瓣持续循环飘落
```

---

## 本地运行

```bash
# 任意 HTTP 服务器均可
python3 -m http.server 8899
# 访问 http://localhost:8899
```
