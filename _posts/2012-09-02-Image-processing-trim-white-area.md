---
layout: post
title: 图像处理之修剪图片周围白色区域
description: "图像处理，去除图片周围白色区域，图片去除白边"
category: "图像处理&验证码识别"
tags: ["图像处理", "验证码识别", "去除白边", "C#"]
---
做验证码识别，建立字模的时候，剪切出来的图片四周常常留有一些空白区域，不能恰好的切割。

如图：

![alt text](/assets/img/6a.png "raw 6")

所以需要想一些方法把四周的白色区域去掉

目标效果：

![alt text](/assets/img/6b.png "6")

有点类似图像的Trim(' ')方法，哈哈，想了半天才想起是有点像字符串处理的Trim方法，刚开始我还起了个ClearWhite(Bitmap bitmap)的蛋疼名字，直接叫Trim(Bitmap bitmap)多好！

先讲下思路，看着上面2张图很容易想到，只需要在原图中找出目标图矩形在原图中的位置就行了。

而画一个矩形，只需要知道矩形左上角的坐标A(x,y)，和矩形的height和width。

所以问题就转换成了在原图中找出A(x,y),height,width。

对着图像四周扫描，判断是否遇到白色的点，就能很容易的找出矩形四个点的坐标。而height可以用左下角坐标点的y减去左上角坐标点的y得出，width可以用右上角坐标点的x减去左上角坐标点的x得出。

上代码：

{% highlight java %}

public static Bitmap Trim(Bitmap bitmap){
            Bitmap resultBmp = null;

            BitmapData bData = bitmap.LockBits(new Rectangle(0, 0, bitmap.Width, bitmap.Height), ImageLockMode.ReadOnly, PixelFormat.Format24bppRgb);

            unsafe
            {
                int width = bitmap.Width;
                int height = bitmap.Height;

                int newx = 0, newy = 0, newHeight = 0, newWidth = 0;

                bool isbreak = false;

                //得到x坐标
                for (int x = 0; x < width; x++)
                {
                    for (int y = 0; y < height; y++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        if (R != 255 || G != 255 || B != 255)
                        {
                            newx = x;
                            isbreak = true;
                            break;
                        }
                    }

                    if (isbreak)
                    {
                        break;
                    }
                }

                isbreak = false;

                //得到y坐标
                for (int y = 0; y < height; y++)
                {
                    for (int x = 0; x < width; x++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        if (R != 255 || G != 255 || B != 255)
                        {
                            newy = y;
                            isbreak = true;
                            break;
                        }
                    }

                    if (isbreak)
                    {
                        break;
                    }
                }

                isbreak = false;

                int tmpy = 0;

                //得到height
                for (int y = height - 1; y >= 0; y--)
                {
                    for (int x = 0; x < width; x++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        if (R != 255 || G != 255 || B != 255)
                        {
                            tmpy = y;
                            isbreak = true;
                            break;
                        }
                    }

                    if (isbreak)
                    {
                        break;
                    }
                }

                isbreak = false;

                newHeight = tmpy - newy + 1;

                int tmpx = 0;

                //得到width
                //得到x坐标
                for (int x = width - 1; x >= 0; x--)
                {
                    for (int y = 0; y < height; y++)
                    {
                        byte* color = (byte*)bData.Scan0 + x * 3 + y * bData.Stride;
                        int R = *(color + 2);
                        int G = *(color + 1);
                        int B = *color;

                        if (R != 255 || G != 255 || B != 255)
                        {
                            Color newColor = Color.FromArgb(R, G, B);
                            tmpx = x;
                            isbreak = true;
                            break;
                        }
                    }

                    if (isbreak)
                    {
                        break;
                    }
                }

                newWidth = tmpx - newx + 1;

                Rectangle rect = new Rectangle(newx, newy, newWidth, newHeight);

                resultBmp = new Bitmap(newWidth, newHeight);

                resultBmp = bitmap.Clone(rect, bitmap.PixelFormat);
            }

            bitmap.UnlockBits(bData);

            return resultBmp;
}


{% endhighlight %}
 
 好吧，代码有点长，求更简洁的方法。
 
 这是关于图像处理的系列文章，后面还有。

{% include JB/setup %}