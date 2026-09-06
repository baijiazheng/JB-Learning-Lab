核心代码：

```
bool Detector::check_geometry(const Lightbar & lightbar) const
{
  auto angle_ok = lightbar.angle_error < max_angle_error_;
  auto ratio_ok = lightbar.ratio > min_lightbar_ratio_ &&
                  lightbar.ratio < max_lightbar_ratio_;
  auto length_ok = lightbar.length > min_lightbar_length_;
  return angle_ok && ratio_ok && length_ok;
}
```

本质上没用AI：利用可利用的先验信息降低问题复杂度。
```
Lightbar
   │
   ├── angle（角度）
   ├── ratio（长宽比=长度/宽度）
   └── length（长度）
          ↓
     三个都通过？
       ↓       ↓
      YES      NO
       ↓        ↓
   保留灯条    丢弃
```