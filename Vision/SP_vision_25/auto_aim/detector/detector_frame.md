# 它的任务就是：从一张图片里找出“可能是装甲板”的灯条组合。

```
bgr_img（原始彩色图像）
        ↓
灰度化(只看亮不亮，不看颜色) cvtColor()
        ↓
二值化（只看黑白）threshold()
        ↓
轮廓提取 findContours()
        ↓
Lightbar（灯条）minAreaRect()（最小外接旋转矩形：用一个可以旋转的矩形包住轮廓）
可以得到：
        center（中心）
        width（宽度）
        height（高度）
        angle（角度）
        ↓
两两配对
        check_geometry()（几何筛选：判断这个轮廓像不像真正的灯条）
        lightbar.color = get_color(bgr_img, contour);（回到RGB图找颜色）
        lightbars.sort(key=lambda x: x.center[0])（按中心点的x坐标排序）
        遍历再判断是否配对：
        ’‘’for (auto left = lightbars.begin();
                left != lightbars.end();
                left++) {

                for (auto right = std::next(left);
                        right != lightbars.end();
                        right++) {‘’‘
        ↓
Armor（装甲板）
        auto armor = Armor(*left, *right);(认为可能是一个装甲板，之后再进行两灯条距离、角度、宽高比、矩形程度筛选)
        ↓
分类器
        armor.pattern = get_pattern(bgr_img, armor);（get_pattern()（提取装甲板图案：截取装甲板 ROI））
        classifier_.classify(armor);（classify()（分类：识别装甲板上的数字/类别））
        再check_name()（检查识别结果是否合理）
        ↓
筛选
        armor.type = get_type(armor);（区分装甲板是大还是小）
        if (!check_type(armor)) continue;（再确认是否有效）
        |
        |已经比较信任了
        ↓
最终 Armor 列表
        armor.center_norm = get_center_norm(bgr_img, armor.center);（get_center_norm()（归一化中心：把图像坐标转换到归一化坐标））
```

所以这套方案这里不是 YOLO 在直接检测装甲板。
它现在走的是传统视觉路线：
OpenCV → 灯条 → 几何匹配 → 装甲板。