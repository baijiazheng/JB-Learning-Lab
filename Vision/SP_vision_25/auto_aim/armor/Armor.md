# 转换为装甲板

## 核心代码区：
```
Armor::Armor(const Lightbar & left, const Lightbar & right)
: left(left), right(right), duplicated(false)
```
把左右两个灯条转换成可以detect的装甲板对象Armor
              Armor
        ┌───────────────┐
        │     center    │
        │               │
        │ ratio         │
        │ side_ratio    │
        │ roll          │
        │ rectangular   │
        └───────────────┘

## 1、装甲板颜色
```
color = left.color; （在detector里面已经搞了两边颜色统一了）
```

## 2、装甲板中心
```
center = (left.center + right.center) / 2; （及两个灯条的两个中心位置的中点）
```

## 3、四个角点
```
points.emplace_back(left.top);
points.emplace_back(right.top);
points.emplace_back(right.bottom);
points.emplace_back(left.bottom);

left.top -------- right.top
   │                  │
   │      Armor       │
   │                  │
left.bottom ----- right.bottom
```
PnP（Perspective-n-Point：通过二维点和已知三维点求目标姿态） 就会需要这种点。

## 4、左灯条到右灯条的向量，及装甲板的宽度
```
auto left2right = right.center - left.center;
auto width = cv::norm(left2right);
```

## 5、取得灯条长度最大值和最小值
```
auto max_lightbar_length =
    std::max(left.length, right.length);

auto min_lightbar_length =
    std::min(left.length, right.length);
```

## 6、描述宽的ratio
```
ratio = width / max_lightbar_length;

两个灯条之间的距离
────────────────
两根灯条中较长的那根
```

## 7、两个灯条的比
```
side_ratio = max_lightbar_length / min_lightbar_length;
```

## 8、roll
```
auto roll =
    std::atan2(left2right.y, left2right.x);（atan2()（根据向量计算方向角：单位为弧度））
```
装甲板左右方向相对于图像水平轴的角度。

## 9、判断左右灯条是不是垂直于装甲板上，并计算误差
```
auto left_rectangular_error =               （left_rectangular_error（左灯条与装甲板方向的垂直误差））
    std::abs(left.angle - roll - CV_PI / 2);

auto right_rectangular_error =
    std::abs(right.angle - roll - CV_PI / 2);
```
## 10、选择最大误差的看是否在范围内，达到判断的目的

```
rectangular_error =
    std::max(
        left_rectangular_error,
        right_rectangular_error
    );

rectangular_error_ok =
    armor.rectangular_error < max_rectangular_error_;
```