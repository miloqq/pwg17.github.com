色彩模型

A color model is an abstract mathematical model describing the way colors can be represented as tuples of numbers, typically as three or four values or color components. When this model is associated with a precise description of how the components are to be interpreted (viewing conditions, etc.), the resulting set of colors is called color space. This section describes ways in which human color vision can be modeled.

色彩模型是一种用来将颜色表示为一组（通常三个或者四个）数字的抽象的数学模型。这样所组成的色彩的集合被称为色彩空间。在这里我们仅仅描述可以模拟人类的色彩视觉模型。

1.Tristimulus color space

One can picture this space as a region in three-dimensional Euclidean space if one identifies the x, y, and z axes with the stimuli for the long-wavelength (L), medium-wavelength (M), and short-wavelength (S) receptors. The origin, (S,M,L) = (0,0,0), corresponds to black. White has no definite position in this diagram; rather it is defined according to the color temperature or white balance as desired or as available from ambient lighting. The human color space is a horse-shoe-shaped cone such as shown here (see also CIE chromaticity diagram below), extending from the origin to, in principle, infinity. In practice, the human color receptors will be saturated or even be damaged at extremely high light intensities, but such behavior is not part of the CIE color space and neither is the changing color perception at low light levels (see: Kruithof curve).

1.三刺激空间

可以描述一个受到长（L）、中（M）、短（S）波长刺激的一点在三维欧几里德空间的x、y、z轴的映射。

（人类可以看到色彩，是因为眼睛感受到了不同波长的光。不同波长的光让人感受到不同的颜色是因为人的视网膜上有一种感光细胞，叫做视锥细胞，视锥细胞不仅可以接收光子，让人看见东西，还可以分辨不同颜色。而人的视锥细胞有三种，可以被长（L）、中（M）、短（S）不同波长刺激激活。三种视锥细胞相当于把一维的光波长映射到三维颜色空间中，三维空间里的每个点对应了一个颜色。）

原点（S，M，L）=（0，0，0）对应到黑色。白色无一定的位置，因为白色是不同色温波长达到平衡后的一种颜色。人的色彩视觉空间，如下面这幅图显示，从原点延伸到无穷远，呈马蹄铁形状。在实践中，人的感光体在强光环境下会饱和甚至会受损，但这样的行为是不属于CIE xyz 色彩空间的，同样，低光照下的色觉改变也不属于。

The most saturated colors are located at the outer rim of the region, with brighter colors farther removed from the origin. As far as the responses of the receptors in the eye are concerned, there is no such thing as "brown" or "gray" light. The latter color names refer to orange and white light respectively, with an intensity that is lower than the light from surrounding areas. One can observe this by watching the screen of an overhead projector during a meeting: one sees black lettering on a white background, even though the "black" has in fact not become darker than the white screen on which it is projected before the projector was turned on. The "black" areas have not actually become darker but appear "black" relative to the higher intensity "white" projected onto the screen around it. See also color constancy.

饱和度向边缘逐渐提高，离原点越远亮度越高。对于人眼中的感光体来说，是没有棕色或者灰色的光的，这两种颜色分别是明亮度不同的橙色和白色。可以观察一次会议期间投影仪的屏幕：我们能看到白色背景上的黑色字母，尽管实际上黑色字母那块屏幕并不是真的比开机前黑，只是黑色比白色亮度低投射到屏幕上的效果。参考 色彩恒常性。

The human tristimulus space has the property that additive mixing of colors corresponds to the adding of vectors in this space. This makes it easy to, for example, describe the possible colors (gamut) that can be constructed from the red, green, and blue primaries in a computer display.

三刺激空间有混合的颜色对应于矢量的性质。这使得它很容易在计算机上用红、绿、蓝三原色显示出任何颜色。

2.CIE XYZ color space

One of the first mathematically defined color spaces is the CIE XYZ color space (also known as CIE 1931 color space), created by the International Commission on Illumination in 1931. These data were measured for human observers and a 2-degree field of view. In 1964, supplemental data for a 10-degree field of view were published.

2.CIE xyz 色彩空间

是其中一个最先采用数学方式来定义的色彩空间（也叫做CIE 1931 xyz 色彩空间），它由国际照明委员会（CIE）于1931年创立。CIE 1931 是色度观察者在2度视场下的观察结果。在1964年又发布了10度视场下的补充数据。

Note that the tabulated sensitivity curves have a certain amount of arbitrariness in them. The shapes of the individual X, Y and Z sensitivity curves can be measured with a reasonable accuracy. However, the overall luminosity function (which in fact is a weighted sum of these three curves) is subjective, since it involves asking a test person whether two light sources have the same brightness, even if they are in completely different colors. Along the same lines, the relative magnitudes of the X, Y, and Z curves are arbitrary. One could as well define a valid color space with an X sensitivity curve that has twice the amplitude. This new color space would have a different shape. The sensitivity curves in the CIE 1931 and 1964 xyz color space are scaled to have equal areas under the curves.

需要注意的是制表的敏感度曲线有一定量的任意性在其中。单独的 X、Y 和 Z 敏感度曲线可以按合理的精度测量。但是整体的光度曲线（它事实上是这个三个曲线的加权和）是主观的，因为它涉及到问测试人两个光源是否有同样的明度，即使它们是完全不同的颜色。同样的，X、Y 和 Z 的曲线的相对大小（magnitude）也是任意的。你也可以定义有两倍幅值的 X 敏感度曲线的有效色彩空间。这个新色彩空间将有不同的形状。CIE 1931 和 1964 XYZ 色彩空间的敏感度曲线被缩放为有相同的曲线下面积。

Sometimes XYZ colors are represented by the luminance, Y, and chromaticity coordinates x and y, defined by:

CIE XYZ 色彩空间中Y参数是明度或者亮度的测量，x和y是色度参数。

Mathematically, x and y are projective coordinates and the colors of the chromaticity diagram occupy a region of the real projective plane. Because the CIE sensitivity curves have equal areas under the curves, light with a flat energy spectrum corresponds to the point (x,y) = (0.333,0.333).

数学上，x 和 y 是投影坐标，色度图的颜色占据了实投影平面的一个区域。由于 CIE 1931 和CIE 1964 敏感度曲线面积相等，所以有一个均等能量点 (x,y) = (0.333,0.333).

The values for X, Y, and Z are obtained by integrating the product of the spectrum of a light beam and the published color-matching functions.

X，Y和Z的值可以通过适应光发光效率函数的积分和颜色匹配函数得到。

3.RGB color model

Media that transmit light (such as television) use additive color mixing with primary colors of red, green, and blue, each of which stimulates one of the three types of the eye's color receptors with as little stimulation as possible of the other two. This is called "RGB" color space. Mixtures of light of these primary colors cover a large part of the human color space and thus produce a large part of human color experiences. This is why color television sets or color computer monitors need only produce mixtures of red, green and blue light. See Additive color.

3.RGB 色彩模型

透射光（如电视）使用红、绿、蓝三原色來生成不同的颜色。三原色的色光以不同的比例相加，以产生多种多样的色光以影响人眼中的三种视锥细胞。这也叫做RGB 色彩空间。这些原色光的混合物覆盖了人类色彩空间的很大一部分，从而产生了很大一部分人对颜色的感受。这就是为什么彩色电视机或电脑彩色显示器只需要红，绿和蓝光。参考 加色法

Other primary colors could in principle be used, but with red, green and blue the largest portion of the human color space can be captured. Unfortunately there is no exact consensus as to what loci in the chromaticity diagram the red, green, and blue colors should have, so the same RGB values can give rise to slightly different colors on different screens.

其他的原色也可以用，但红、绿、蓝三原色覆盖人来色彩空间最广。不幸的是，不同设备对特定RGB值的检测和重现都不一样，因为颜色物质（荧光剂或者染料）和它们对红、绿和蓝的单独响应水平随着制造商的不同而不同，甚至是同样的设备不同的时间也不同。

HSV and HSL representations

Recognizing that the geometry of the RGB model is poorly aligned with the color-making attributes recognized by human vision, computer graphics researchers developed two alternate representations of RGB, HSV and HSL (hue, saturation, value and hue, saturation, lightness), in the late 1970s. HSV and HSL improve on the color cube representation of RGB by arranging colors of each hue in a radial slice, around a central axis of neutral colors which ranges from black at the bottom to white at the top. The fully saturated colors of each hue then lie in a circle, a color wheel.

HSV 和 HSL 表示的色彩空间

认识到RGB模型不足与人类视觉识别的颜色相一致，20世纪70年代中后期，计算机图形学的研究人员开发了两个RGB模型的替代表示，HSV和HSL（色相，饱和度和色相，饱和度，亮度）。HSL 和 HSV 二者都把颜色描述在圆柱坐标系内的点，这个圆柱的中心轴取值为自底部的黑色到顶部的白色而在它们中间是的灰色，绕这个轴的角度对应于“色相”，到这个轴的距离对应于“饱和度”，而沿着这个轴的高度对应于“亮度”，“色调”或“明度”。

HSV models itself on paint mixture, with its saturation and value dimensions resembling mixtures of a brightly colored paint with, respectively, white and black. HSL tries to resemble more perceptual color models such as NCS or Munsell. It places the fully saturated colors in a circle of lightness ½, so that lightness 1 always implies white, and lightness 0 always implies black.

HSV 模型本身 是色彩的混合物，













