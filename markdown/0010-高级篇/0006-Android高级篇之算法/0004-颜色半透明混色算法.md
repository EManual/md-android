各点像素的RGB值是按一定比例混合而成的，这个比例由Alpha值决定，具体算式见下：
```    
Alpha = 0 ~ 100 
R = ( R1 * (100 - Alpha) + R2 * Alpha ) / 100 
G = ( G1 * (100 - Alpha) + G2 * Alpha ) / 100 
B = ( B1 * (100 - Alpha) + B2 * Alpha ) / 100 
示例： 
RGB1 ( 232, 54, 13 ) 
RGB2 ( 94, 186, 233 ) 
Alpha = 30 
R = ( 232 * 70 + 94 * 30 ) / 100 = 190.6 -> 190 
G = ( 54 * 70 + 186 * 30 ) / 100 = 93.6 -> 93 
B = ( 13 * 70 + 233 * 30 ) / 100 = 79 
Alpha = 50 
R = ( 232 * 50 + 94 * 50 ) / 100 = 163 
G = ( 54 * 50 + 186 * 50 ) / 100 = 120 
B = ( 13 * 50 + 233 * 50 ) / 100 = 123 
32位色下的颜色混合 
R = R1 * Alpha1 + R2 * Alpha2 * (1-Alpha1) 
G = G1 * Alpha1 + G2 * Alpha2 * (1-Alpha1)   
B = B1 * Alpha1 + B2 * Alpha2 * (1-Alpha1) 
Alpha = 1 - (1 - Alpha1) * ( 1 - Alpha2) 
R = R / Alpha 
G = G / Alpha 
B = B / Alpha  
``` 
R1、G1、B1、Alpha1指上层的颜色值。
R2、G2、B2、Alpha2指下层的颜色值。
R、G、B、Alpha指合并后的颜色。