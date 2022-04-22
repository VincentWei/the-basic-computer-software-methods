## 多媒体：图像、图形及音视频

- 多媒体及计算机输出设备
- 图像
- 矢量图形
- 动画：基于静止图像或基于矢量图形
- 音频
- MIDI：矢量化音频
- 视频压缩原理

		
## 1. 多媒体及计算输入输出设备

- 多媒体（multimedia）
   - 图像（image）及图形（graphics）
   - 音频（audio）
   - 视频（video）

	
- 视觉输入输出设备
   - 字符终端（character terminal）
   - 图形终端（graphics terminal）
   - 摄像头（camera）

	
- 听觉输入/输出设备
   - 麦克风（microphone）
   - 扬声器（speaker）
   - 耳机（earphone）

	
- 其他输入/输出设备
   - 陀螺仪（gyroscope）
   - 振动器（vibrator）

		
## 2. 显示器、显存及帧缓冲区

- 显示器（display）
- 显存（video RAM、VRAM）
- 帧缓冲区（frame buffer）

		
## 3. 静态图像的表述

- 用位图（bitmap/pixmap）：像素点组成的矩阵
  - 像素（pixel）
  - 每像素位数（bits per pixel, color depth），bpp
  - 每像素字节数（bytes per pixel, color depth），Bpp
  - 每行占用的字节数（pitch）
  - 透明像素或每像素的 Alpha 值

		
## 4. 像素值的两种记录方式

1. 调色板（palette）
   - 像素值用调色板中的颜色编号表示。
   - 适合于单色、四位色、八位色
1. 直接记录 RGBA 分量
   - 使用 16 位、24 位或者 32 位来记录 RGBA 分量

		
## 5. 静态图像显示到屏幕上的过程

1. 读取图像文件头部，获取基本信息（高度、宽度、色深等）
1. 按照图像文件的编码方式解码像素值，生成设备无关位图。
1. 将设备无关位图转换成适合屏幕当前像素格式的设备相关位图。
1. 将设备相关位图复制到帧缓冲区中。

		
## 6. 其他色彩空间

- YUV：通过亮度（luminance）、色调和饱和度定义颜色（即色度）。
- YIQ：和 YUV 类似，用于美式电视信号标准 NTSC。
- CMYK：主要用于打印机和印刷业，通过青、洋红、黄和黑四种颜色的不同取值来定义一种颜色。

	
- 颜色空间转换

```
Y = 0.30R + 0.59G + 0.11B
U = (B − Y) × 0.493
V = (R − Y) × 0.877

Y = 0.30R + 0.59G + 0.11B
I = 0.60R − 0.28G − 0.32B
Q = 0.21R − 0.52G + 0.31B
```

		
## 7. 常见的图像文件格式

- 静止图像的基本编码方式
  - 无损压缩（调色板、RLE、特定的压缩算法）
  - 有损压缩（JPEG）

	
- GIF
  - 基于调色板，只能包含最多 256 种颜色
  - LZW 算法，一种无损压缩算法
  - 支持透明像素
  - 支持基于多帧静止图片的动画

	
- PNG
  - 为替代 GIF 而生，支持多种像素格式
  - 支持 Alpha 分量
  - 使用基于 LZ77 的一种派生压缩算法

	
- JPEG
  - 支持 RGB、YUV 及灰度色彩空间
  - 同时支持无损和有损压缩

	
- 其他图像格式
   - WebP，Google 定义，派生自 VP8 视频编码技术
   - BMP，微软为 Windows 定义的位图格式
   - TIFF，多用于扫描仪和传真机

		
## 8. 抖动算法

使用调色板中可用颜色的扩散来获得近似效果，此时人眼会自动将扩散的不同颜色混合成新的颜色。

	
![色带效果](assets/chapter-5-1.png)

	
![抖动原理](assets/chapter-5-2.png)

	
![抖动效果](assets/chapter-5-3.png)

		
## 9. 矢量图形

- 分清图像（image）和图形（graphics）的区别
- 矢量图形：使用数学方法描述图形的轮廓和填充属性

	
- SVG
   - 支持矢量图形对象，包括矩形、圆、椭圆、多边形、直线、任意曲线以及填充方式等。
   - 支持嵌入式外部图形，包括 P NG、JPEG 等外部图像以及外部 SVG 等。
   - 支持文字对象。
   - 支持各种滤镜和特殊效果。
   - 可嵌入 HTML 页面，支持动画。

	
- SVG 样例

```svg
<?xml version="1.0"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"
    "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg xmlns="http://www.w3.org/2000/svg" version="1.1"
        width="467" height="462">
    <!-- 红色矩形 -->
    <rect x="80" y="60" width="250" height="250" rx="20"
            fill="red" stroke="black" stroke-width="2px" />
    <!-- 蓝色矩形 -->
    <rect x="140" y="120" width="250" height="250" rx="40"
            fill="blue" stroke="black" stroke-width="2px" fill-opacity="0.7" />
</svg>
```
	
- 渲染效果

![矢量图形](assets/chapter-5-4.svg)

		
## 10. 基于静止图像的动画

- GIF89a
   - 文件中包含多帧使用 GIF 编码定义的图像，轮换播出。
   - 除了第一帧之外，其他帧可定义局部变化。
- MJPEG
   - 有多个同样分辨率的连续 JPEG 图像组成

		
## 11. 基于矢量图形的动画

```svg
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"
    "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="8cm" height="3cm" viewBox="0 0 800 300"
        xmlns="http://www.w3.org/2000/svg" version="1.1">
    <desc>演示 animation 元素</desc>
    <rect x="1" y="1" width="798" height="298"
        fill="none" stroke="blue" stroke-width="2" />

    <!-- 下面的代码描述了如何使用 animation 元素来定义一个动画。
        这些 animation 元素分别改变矩形的 x、y、width、height 四个属性，
        最终使之充满整个视口 -->
    <rect id="RectElement" x="300" y="100" width="300" height="100"
            fill="rgb(255,255,0)" >
        <animate attributeName="x" attributeType="XML"
                begin="0s" dur="9s" fill="freeze" from="300" to="0" />
        <animate attributeName="y" attributeType="XML"
                begin="0s" dur="9s" fill="freeze" from="100" to="0" />
        <animate attributeName="width" attributeType="XML"
                begin="0s" dur="9s" fill="freeze" from="300" to="800" />
        <animate attributeName="height" attributeType="XML"
                begin="0s" dur="9s" fill="freeze" from="100" to="300" />
    </rect>

    <!-- 设立一个新的用户坐标系统，使得文本字符串的原点位于(0,0)，
        从而相当于新的原点旋转和放大字符串文本。-->
    <g transform="translate(100,100)" >
        <!-- 下面的代码演示了 set、animateMotion、
            animate 和 animateTransform 元素。text 元素最初
            是隐藏的（也就是不可见）。在第三秒时：
                * 变为可见
                * 沿视口的对角线连续移动
                * 其颜色从蓝色向深红色变化
                * 从 -30 度向零度（水平）旋转
                * 将字号放大三倍。-->
        <text id="TextElement" x="0" y="0"
                font-family="Verdana" font-size="35.27" visibility="hidden" >
                It's alive!
            <set attributeName="visibility" attributeType="CSS" to="visible"
                    begin="3s" dur="6s" fill="freeze" />
            <animateMotion path="M 0 0 L 100 100"
                    begin="3s" dur="6s" fill="freeze" />
            <animate attributeName="fill" attributeType="CSS"
                    from="rgb(0,0,255)" to="rgb(128,0,0)"
                    begin="3s" dur="6s" fill="freeze" />
            <animateTransform attributeName="transform" attributeType="XML"
                    type="rotate" from="-30" to="0"
                    begin="3s" dur="6s" fill="freeze" />
            <animateTransform attributeName="transform" attributeType="XML"
                    type="scale" from="1" to="3" additive="sum"
                    begin="3s" dur="6s" fill="freeze" />
        </text>
    </g>
</svg>
```

	
- 渲染效果

![基于矢量图形的动画](assets/chapter-5-5.svg)

		
## 12. 音频信号的量化处理

音频数字化后的保真度取决两个因素：

1. 采样频率
1. 采样值量化位数

人耳可以听到的声音频率范围在 20Hz 到 20KHz

	
1. 电话质量
   - 8000 Hz 的采样频率
   - 8 位的采样值量化位数
   - 领导半个小时的讲话，则其原始的音频量化数据大小为：8000 * 60 * 30 = 14,400,000B，约为 13.4 MiB
1. CD 质量
   - 44100 Hz 的采样频率
   - 16 位的量化位数
   - 半个小时的交响乐演奏以 CD 质量、双声道（立体声）做量化处理，则原始数据大小为：44100 * 2 * 60 * 30 * 2 = 317,520,000，约为 302 MB

		
## 13. 音频量化数据的压缩手段

1. 采样阶段完成，无损压缩
1. 对量化后的数据使用专用于音频数据的无损压缩算法，如 FLAC。
1. 将时域数据基于快速傅里叶变换或者小波变换而成为频域数据，仅记录重要的频率和振幅，属于有损压缩。

		
## 14. 采样阶段的无损压缩

- PCM：脉冲码调制（Pulse Code Modulation），原始数据。
- DPCM：差分脉冲码调制（Differential Pulse Code Modulation），约 50% 的压缩率。
- ADPCM：自适应差分脉冲码调制（Adaptive Difference Pulse Code Modulation），约 25% 的压缩率。

		
## 15. MIDI

- 类似矢量图形，我们可以将 MIDI 理解为一种音频的“矢量化”处理方法。
- MIDI：乐器数字接口（Musical Instrument Digital Interface）。
- 由美国加州的音乐人 Dave Smith 于 20 世纪 80 年代发明。

	
只要知道了音高、强度 、节奏（快慢和时值）以及音色（对应的谐振波组成），就可以使用数字方法合成出对应的音频信号。

		
## 16. MIDI 音色的两种实现方法

1. 合成谐振波合成某种音色，缺点是音色的还原度低，优点是简单，存储量小。
1. 波表合成：事先记录特定乐器之不同音符对应的音频信号，然后再根据每个音轨对应的音符强弱和时长合成在一起播放。

		
## 17. 视频的相关概念

- 清晰度（definition）
  - 高清电视（HDTV，High Definition TV）的水平扫描线可达到 1080 条，每条扫描线上有 1920 个像素点，对应的屏幕分辨率为 1920x1080；
  - 超高清电视（Ultra HDTV，俗称的 “4K”），对应的屏幕分辨率为 3840×2160。
- 帧率（frames per second）
  - 胶片电影：24 fps。
  - PAL 制式：25fps。
  - NTSC 制式 29.97fps。
- 扫描模式：
  - 交错扫描（Interlaced Scan），也称隔行扫描。
  - 逐行扫描（Progressive Scan）。

		
## 18. 视频压缩原理

- 帧内压缩：比如 Motion JPEG，将每一帧静态图像利用 JPEG 技术进行压缩。
- 帧间压缩：第一帧画面的完整图像，然后对其后的每一帧，只记录和前一帧的差异数据，类似音频处理中的差分脉冲编码。
- 码率（bit rate）：视频内容的清晰度、帧率以及所采取的压缩技术，我们可以确定一个流畅展现一段视频内容所需要的最高传输速度。

	
播放分辨率为 1080p、帧率为 25 fps 的高清视频，采用 H.264 编码技术，尖峰时候的网络带宽要求大概为 10 Mbps。

## 19. 实例分析：MPEG-1 编码技术

- YUV 色彩空间
- 每一帧图像由三个部分组成：
   1. 亮度
   1. 色调
   1. 饱和度
- 亮度部分像素点数量在水平和垂直方向是色调和饱和度的两倍。

	

三种帧类型：
1. I-Frame（Intra-coded Image，内编码图像）。
1. P-Frame（Predictive-coded Image，预测编码图像）。记录的是其相对于之前的 I-Frame（以及其他前置 P-Frame）的变化部分。
1. B-Frame（Bi-directionally Predictive-coded Image，双向预测编码图像）。前一张静态图像和后续 I-Frame 或 P-Frame 之间的差异。

实践中 MPEG-1 的不同类型图像编码以下面的顺序出现：IBBPBBPBBIBBPBBPBB…
