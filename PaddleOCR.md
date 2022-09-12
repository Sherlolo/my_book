# PaddleOCR

## 数据集

lsvt，rctw，CASIA，CCPD，MSRA，MLT，BornDigit，iflytek，SROIE和合成的数据集

总数据量越10W，数据集分为5个部分，训练时采用随机采样策略，在4卡V100GPU上约训练500epoch，耗时3天。