显示系统由两部分组件构成，一个是显示屏驱动组件，将需要显示的内容发送的显示屏；另一个是绘图组件，用来向显示屏的内存空间中绘制图像。

本图形库(Gui Lib)属于绘图组件，它提供了绘制，点、线、面、文字等常用的绘图功能。当一幅图形绘制好之后，系统调用显示屏驱动，将其发送到显示屏。

> 本系列API目前仅支持nRF51平台。

对于nRF51平台的用户，使用时请引用相应的头文件：

* API头文件：[juma_sdk_api.h](https://github.com/JUMA-IO/nRF51_Platform/blob/master/Interface/Include/juma_sdk_api.h)
* 数据类型或宏定义参见：[juma_sdk_types.h](https://github.com/JUMA-IO/nRF51_Platform/blob/master/Interface/Include/juma_sdk_types.h)


***
## 初始化显示区域
###1. 函数声明
```
void gfx_init_screen(uint8_t* buffer, uint16_t width, uint16_t height, uint8_t flags);
```

###2. 函数功能
图形库的初始化，具体操作是为图形库提供一段内存，并指明显示屏的尺寸。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*buffer*|uint8_t * |显示内容的内存地址
*width*|uint16_t|显示屏的宽（像素点）
*height*|uint16_t|显示屏的高（像素点）
*flags*|uint8_t|对显示器的一些配置
*返回值*  | 无    | 无

####关于 *flags* 的描述
位|7|6|5|4|3|2|1|0
:----|:---|:---|:---|:---|:---|:---|:---|:---
功能|X|X|X|X|X|X|X|A

`A`为屏幕上的像素点内容：

- `1`：表明置1的时候为点亮像素
- `0`：表明置0的时候为点亮像素

###4. 函数举例

```		
	//当前函数
	void foo()
	{
		...
		//调用本函数将初始化图形库
		/* 显示内存区域的起始地址为buff，其宽度为128像素点，其高度为64像素点 */
		gfx_init_screen(buff, 128, 64, 0);
		...
	}
```

***
## 清空显示区域
###1. 函数声明
```
void gfx_clear(void);
```

###2. 函数功能
清空显示区域的内容，将像素值设置为[`A`](#_1)。

###3. 函数参数
###4. 函数举例

```	
	
	//当前函数
	void foo()
	{
		...
	
		/*现有一个与之关联的显示区域，
		buff = {
			0x55, 0xaa, 0x11...
		};*/
	
		//调用本函数将显示区域中的数据清空
		gfx_clear();
		
		/*显示区域的数据根据在gfx_init_screen中配置的flags来对清空操作
		如果flags中的A位（关于flags中的位定义请参看gfx_init_screen中的描述）
		
		情况1：如果在flags中配置写1为点亮，那么这里会被清除为 
		buff = {
			0x00, 0x00, 0x00....
		}；
			
		情况2：如果在flags中配置为写0为点亮，那么这里会被清除为
		buff = {
			0xff, 0xff, 0xff ...
		};
			
		*/
	
		...
	}
	
```


***
## 设定字体
###1. 函数声明
```
void gfx_set_font(const gfx_font_t* font);
```

###2. 函数功能
设定显示的文字字体。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*font*|const gfx_font_t * |字体类型
*返回值*  | 无    | 无


###关于 *font* 的描述
该参数所代表的含义是字体的类型，字体的实现参见库文件[TBD]，其一般形式为:

```
	const gfx_font_t Arial_9 = {
   		Arial_9_Table, 3,
    	12, 12,
    	33, 127,
	};

```

###4. 函数举例
```	
	void foo()
	{
		...	
		//设定绘图库中字体类型
		gfx_set_font(&Arial_9);
		...
	}
```

***
## 绘制字符
###1. 函数声明
```
uint16_t gfx_draw_char(uint16_t x, uint16_t y, char c, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制字符。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|要绘制字符的起始左上角坐标的x位置
*y*|uint16_t|要绘制字符的起始左上角坐标的x位置
*c*|char|要显示的字符本身
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  |uint16_t|当前的字符在显示区域中所占据的宽度

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4.函数举例

```	
	void foo()
	{
		...
		//在显示共享区中根据ppo的取值绘制一个起始左上角坐标为(x,y)字符为'c'的字符
		gfx_draw_char(x, y, c, ppo);
		...
	}
```

***
## 绘制像素点
###1. 函数声明
```
void gfx_draw_pixel(uint16_t x, uint16_t y, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制一个像素点。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|显示位置的x坐标
*y*|uint16_t|显示位置的y坐标
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//对显示共享区中显示坐标为(1,1)的点进行操作
		gfx_draw_pixel(1,1,0)；	//清除操作
		...
	}
```

***
## 绘制一条线
###1. 函数声明
```
void gfx_draw_line(uint16_t x0, uint16_t y0, uint16_t x1, uint16_t y1, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制一条线。


###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x0*|uint16_t|线的起始位置坐标x
*y0*|uint16_t|线的起始位置坐标y
*x1*|uint16_t|线的结束位置坐标x
*y1*|uint16_t|线的结束位置坐标y
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无


####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//对显示共享区中起始坐标为(1,1)，终止坐标为(10,10)上的点进行操作
		gfx_draw_line(1,1,10,10, 0)；	//清除操作
		...
	}
```



***
## 绘制圆框
###1. 函数声明
```
void gfx_draw_circle(uint16_t x, uint16_t y, uint16_t r, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制一个圆框。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|显示的圆的圆心位置的x坐标
*y*|uint16_t|显示的圆的圆心位置的y坐标
*r*|uint16_t|圆的半径
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//在显示共享区中对一个圆心坐标为(x,y)半径为r的框根据ppo的取值对其进行操作.
		gfx_draw_circle(x, y, r, ppo);
		...
	}
```

***
## 绘制矩形框
###1. 函数声明
```
void gfx_draw_rect(uint16_t x, uint16_t y, uint16_t w, uint16_t h, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制一个矩形框。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|显示位置的x坐标
*y*|uint16_t|显示位置的y坐标
*w*|uint16_t|矩形框的宽
*h*|uint16_t|矩形框的高
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//在显示共享区中对一个坐标为(x,y)宽度为w，高度为h的矩形框形状的区域根据ppo的取值来进行操作
		gfx_draw_rect(x, y, w, h, ppo);
		...
	}
```

***
## 填充圆框
###1. 函数声明
```
void gfx_fill_circle(uint16_t x, uint16_t y, uint16_t r, uint8_t ppo);
```

###2. 函数功能
在显示区域填充一个圆框。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|显示的圆的圆心位置的x坐标
*y*|uint16_t|显示的圆的圆心位置的y坐标
*r*|uint16_t|圆的半径
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//在显示共享区中对一个圆心坐标为(x,y)半径为r的圆形区域根据ppo的取值对其进行操作.
		gfx_fill_circle(x, y, r, ppo);
		...
	}
```

***
## 填充矩形框
###1. 函数声明
```
void gfx_fill_rect(uint16_t x, uint16_t y, uint16_t w, uint16_t h, uint8_t ppo);
```

###2. 函数功能
在显示区域填充一个矩形框。

###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|显示位置的x坐标
*y*|uint16_t|显示位置的y坐标
*w*|uint16_t|矩形框的宽
*h*|uint16_t|矩形框的高
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//在显示共享区中对一个坐标为(x,y)宽度为w，高度为h的矩形区域形状的区域根据ppo的取值来进行操作
		gfx_fill_rect(x, y, w, h, ppo);
		...
	}
```

***
## 绘制指定图片
###1. 函数声明
```
void gfx_draw_image(uint16_t x, uint16_t y, const uint8_t* image, uint16_t width, uint16_t height, uint8_t ppo);
```

###2. 函数功能
在显示区域绘制指定图片。


###3. 函数参数
参数    | 数据类型   | 说明
:----- | :-------- | :------
*x*|uint16_t|图片显示位置的x坐标
*y*|uint16_t|图片显示位置的y坐标
*image*|const uint8_t * |图片本身的存储地址
*width*|uint16_t|图片的宽
*height*|uint16_t|图片的高
*ppo*|uint8_t|对显示区域中的位的操作方式
*返回值*  | 无    | 无

***
###关于 *image* 的描述
`image`代表着图片本身，图片的存储方式为：<br>
按位横向展开，byte之间采取小段字节序，bit之间采取msb方式。只有满足这种格式存储的图片才能使用这里的绘图函数显示。如果存储好的图片显示出来排版有偏差，请修改存储格式，或者检查width和height的设置。

####关于 *ppo* 的描述
`ppo`的取值范围为：<br>
`0`: 清空操作<br>
`1`: 写操作<br>
`2`: 取反操作 <br>  

###4. 函数举例
```	
	void foo()
	{
		...	
		//在显示共享区中在坐标为(x,y)按照ppo的要求，显示一个宽为weight高为height的已经准备好的图片。
		gfx_draw_image(x, y, image, w, h, ppo);
		...
	}
```





