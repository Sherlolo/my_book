# opencv C++

标签（空格分隔）： project

---

# 基础操作

## 创建矩阵并指定类型

```
#转换类型
cv::Mat matTemp = cv::Mat::zeros(100,100,CV_32F); //得到一个浮点型的100*100的矩阵
cv::Mat MatTemp2;
matTemp.convertTo(MatTemp2, CV_8U); //把矩阵matTemp转为unsing char类型的矩阵，注在转换过程中有可能数值上会出现一些变化，这个要注意
```

## ncnn和cv矩阵的相互转换

```c++
//cv::mat -> ncnn::mat
cv::Mat in_matc;
ncnn::Mat input = ncnn::Mat::from_pixels(in_matc.data, ncnn::Mat::PIXEL_BGR, in_matc.cols, in_matc.rows);

//ncnn::mat -> cv::mat
ncnn::Mat& in_matn;
cv::Mat input_c0(in_matn.h, in_matn.w, CV_32FC1, (void*)(const float*)in_matn.channel(0));

//ncnn::Mat 3 channel -> cv::Mat CV_8UC3 + swap RGB/BGR
// ncnn::Mat in(w, h, 3);
cv::Mat a(in.h, in.w, CV_8UC3);
in.to_pixels(a.data, ncnn::Mat::PIXEL_BGR2RGB);

// ncnn::Mat in(w, h, 1);
cv::Mat a(in.h, in.w, CV_8UC1);
in.to_pixels(a.data, ncnn::Mat::PIXEL_GRAY);
```

# 常用操作

## cv::copyMakeBorder

```c++
//top指的是上边界往上扩招的行数
copyMakeBorder(
    Mat src,    //输入图像
    Mat dst,    //输出图像
    int top,        
    int bottom,
    int left, 
    int right,
    int borderType, //边缘类型
    Scalar color
)

//eg

cv::copyMakeBorder(matn, temp,
                           dh, netH - h - dh, dw, netW - w - dw,
                           cv::BorderTypes::BORDER_CONSTANT,
                           cv::Scalar(127, 127, 127));
```

