1. cv2打开和保存图片:分彩色和灰度图两种情况  
    (1)读入指令为cv2.imread('house.jpg')，cv2.imread('house.jpg',cv2.IMREAD_GRAYSCALE)  
    (2)读入图像为nd.array格式矩阵，注意颜色频道BGR的顺序，  
    (3)保存指令为cv2.imwrite('imgsavename.*',imgname)。
    如果显示图像时做了BGR2RGB，则在保存前应该做RGB2BGR，否则存的图频道颠倒。  
2. PIL打开和保存图片：  
    (1)打开文件指令为Image.open('house.jpg')，Image.open('house.jpg').convert('L')   
    (2)打开后图像格式为PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=280x200，  
      dtype为JPEG，mode为RGB；灰度图则分别为None和mode=L；  
    (3)需要使用nd.array()转换为array格式的矩阵，  
    (4)保存时再用Image.fromarray()转换回图像<PIL.Image.Image image mode=RGB size=280x200,dtype为None  
    (5)保存指令为imgname.save('imgsavename.*')。  
3. skimage打开和保存图片:分彩色和灰度图两种情况  
    (1)读入彩色图像指令为 io.imread('house.jpg')，  
      读入图片的数据格式为uint8，三通道RGB格式，与cv2同  
    (2)读入灰度图像指令为io.imread('house.jpg',as_grey=True),  
      读入图片的数据格式为float，数值在0到1之间  
4. plt打开和保存图片:  
    (1)读入指令为plt.imread()，  
    (2)保存指令为plt.savefig('imgsavename.*'),且一定在plt.show()之前；  
      plt.savefig将当前窗口内的内容保存，包括白边(有无参数设置为不保存白边？)。  
[原文](https://blog.csdn.net/LiuKejiaHAX/article/details/80711208)
