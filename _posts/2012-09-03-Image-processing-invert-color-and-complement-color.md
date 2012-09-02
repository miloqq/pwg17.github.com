---
layout: post
title: 图像处理之反色和补色
description: 图像处理，图片反色、对比色、补色处理、图像反相
category: "图像处理&验证码识别"
tags: ["图像处理", "验证码识别", "C#"]
---

颜色有三大属性：明暗，即明度；彩调，即色相；色强，即纯度。

`明度`：色彩的明暗程度。两种情况，一是同一色相的明度变化，同一颜色加黑、白以后产生不同的明暗层次；二是各种颜色的明度变化，每种纯色都有与之对应的明度，黄色明度最高，蓝紫色最低，红绿色居中。 

`色相`：色彩的相貌。是有彩色最显著的特征，是指能够比较确切的表示某种颜色色别的名称。物体的颜色是由光源的色谱成份和物体表面反射的特征决定的。在可见光谱上，人的视觉能够感受不同特征的色彩，人们给这些相互区别的色定出名称，如红、橙、黄、绿、蓝、紫等颜色的色族，就会有一个特定的色彩印象，这就是色相的概念。 

`纯度`：色彩的纯净程度。表示颜色中所含某一色彩的成分比例。一般用水平横轴表示纯度。我们的视觉能辨认出的有色相感的色，都具有一定程度的鲜艳度，比如红色，以无色彩为基轴，当其加入白色时，就变成了粉红色；当其加入黑色时，就变成了深红色；当其加入黑色时，其明度没有改变，而纯度降低了。从而我们知道：越靠近无色彩，则纯度越低，色越浊，越灰；越靠近色相环的色彩则纯度越高，色彩越鲜艳。 


####十二色相环:
白色光包含了所有的可见颜色，我们看到是由紫到红之间的无穷光谱组成的可见光区域，就象你所看到的彩虹颜色。为了在使用颜色时更加实用，人们对它进行了简化，将它们分为 12 种基本的色相,如图：

![alt text](/assets/img/12color.png "12色相环")  

色相环由 12 种基本的颜色组成。首先包含的是色彩三原色（Primary colors），即红、黄、蓝。原色混合产生了二次色（Secondary colors），用二次色混合，产生了三次色（tertiary colors）。
原色是色相环中所有颜色的“父母”。在色相环中，只有这三种颜色不是由其它颜色混合而成。三原色在色环中的位置是平均分布的，

####二十四色相环
![alt text](/assets/img/24color.jpg "24色相环")

二十四色相环的基本色相为黄、橙、红、紫、蓝、蓝绿、绿、黄绿8个主要色相，每个基本色相又分为3个部分，组成24个分割的色相环，从1号排列到24号。

####以二十四色相环为例：`正对120度角的是补色，180度角的是反色`

计算公式分别是：

`反色`:

CNew = 255 - COld

`补色`:
CNew = Max(R,G,B) + Min(R,G,B) - COld

一种颜色反色后，颜色的色相值与反相前相差180度，这是反色和补色的共同点。不同点是，反色除了要改变色相以外，纯度和明度也做出了相应的改变，而补色不改变纯度和明度。例如，黑色的反色是白色，黑色的补色还是黑色。

------
好，以上是基础，下面上代码：

`反色`:

{% highlight csharp %}

	public void InvertColor(Bitmap bitmap)
        {
            BitmapData bData = bitmap.LockBits(new Rectangle(0, 0, bitmap.Width, bitmap.Height), ImageLockMode.ReadWrite, PixelFormat.Format24bppRgb);

            unsafe
            {
                int width = bitmap.Width;
                int height = bitmap.Height;

                for (int x = 0; x < width; x++)
                {
                    for (int y = 0; y < height; y++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        color[0] = (byte)(255 - B);
                        color[1] = (byte)(255 - G);
                        color[2] = (byte)(255 - R);
                    }
                }
            }

            bitmap.UnlockBits(bData);
        }  
{% endhighlight %}       
    
`补色`:

{% highlight csharp %}

	public void ComplementColor(Bitmap bitmap)
        {
            BitmapData bData = bitmap.LockBits(new Rectangle(0, 0, bitmap.Width, bitmap.Height), ImageLockMode.ReadWrite, PixelFormat.Format24bppRgb);

            unsafe
            {
                int width = bitmap.Width;
                int height = bitmap.Height;

                for (int x = 0; x < width; x++)
                {
                    for (int y = 0; y < height; y++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        int max = GetMax(R, G, B);
                        int min = GetMin(R, G, B);

                        color[0] = (byte)((max + min) - B);
                        color[1] = (byte)((max + min) - G);
                        color[2] = (byte)((max + min) - R);
                    }
                }
            }

            bitmap.UnlockBits(bData);
        }
        
        private int GetMax(int r, int g, int b)
        {
            int max;

            max = Math.Max(r, g);
            max = Math.Max(max, b);

            return max;
        }

        private int GetMin(int r, int g, int b)
        {
            int min;

            min = Math.Min(r, g);
            min = Math.Min(min, b);

            return min;
        }
{% endhighlight %}

效果图：

`原图直出`：

![alt text](/assets/img/color1.bmp "原图")

`反色效果`:

![alt text](/assets/img/color2.jpg "反色效果")

`补色效果`:

![alt text](/assets/img/color3.jpg "补色效果")

------------

在验证码识别中，建字模的时候，利用反色的原理还可以做在线编辑的效果，鼠标点击一下杂点变白，再点一下又变黑。如图：

和其它字母黏在一起的数字7：![alt text](/assets/img/7a.png "原图")，处理完后的靓仔7：![alt text](/assets/img/7b.png "效果图")

{% include JB/setup %}