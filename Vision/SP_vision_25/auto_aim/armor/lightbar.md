# 把白色轮廓的矩形分析为数据结构

Lightbar
├── center       ← 中心
├── top          ← 顶部中心
├── bottom       ← 底部中心
├── width        ← 宽度
├── length       ← 长度
├── angle        ← 方向
├── angle_error  ← 与竖直方向的误差
└── ratio        ← 长宽比

## 1、获取四个角点
```
std::vector<cv::Point2f> corners(4); （RotatedRect（旋转矩形：OpenCV 用于表示倾斜矩形））
rotated_rect.points(&corners[0]); （points()（获取矩形四个顶点））
```

## 2、按y坐标升序排序，并获取中点

```
std::sort(corners.begin(), corners.end(),
    [](const cv::Point2f & a, const cv::Point2f & b) {
        return a.y < b.y;
    });
```
top = (corners[0] + corners[1]) / 2;
top（顶部中心：上面两个角点的中点）

bottom = (corners[2] + corners[3]) / 2;
bottom（底部中心：下面两个角点的中点）

## 3、获得灯条方向以及长度
```
top2bottom = bottom - top;（top2bottom（顶部指向底部的向量：表示灯条方向））
length = cv::norm(top2bottom);（norm()（计算向量长度））
```

## 4、获得灯条长、宽、长宽比
```
width = cv::norm(corners[0] - corners[1]);（cv::norm()（计算向量长度））
length = cv::norm(corners[2] - corners[3]);（cv::norm()（计算向量长度））
ratio = length / width;（长宽比）
```

## 5、获得灯条角度
```
angle = std::atan2(
    top2bottom.y,
    top2bottom.x
);
```

## 6、获得灯条角度误差
```
angle_error = std::abs(angle - CV_PI / 2); （CV_PI=180/则角度误差：灯条方向与竖直方向的差值）
```
实际上就是：
这个灯条不能歪得太厉害。