# run styleGAN on cpu patchs
修改 dnnlib/tflib/network 网络执行模块，通过加载模型自带的code运行

hack时，取代exec函数，执行网络stylegan\training\networks_stylegan.py

大量修改networks_stylegan源码

# 资源 
*.patch 为补丁，.py为修改源码，替换即可

png.png，生成器网络架构

network_arch.bat，生成网络层原信息输出

Demo_9407354600621004326.png 生成的动漫美少女

v2-a9977f61a73fdb811298bddffb5ca63c_r.jpg 随便扒的一个styleGAN架构，转自styleGAN论文

# 口区
坑爹的运算格式。。。谷歌您啊就不能在CPU版本写个自动NCHW转NHWC吗？？

# reference
https://zhuanlan.zhihu.com/p/31988761
https://github.com/igul222/improved_wgan_training/issues/11
https://zhuanlan.zhihu.com/p/25929909

# notebook

w = Gs.get_var('G_synthesis/128x128/Conv0_up/weight')
w1 = tf.transpose(w,[0,2,3,1]).eval()

try:
	x = tf.transpose(x, [0,2,3,1], name='NCHW_to_NHWC')
	os = tf.transpose(os, [0,3,1,2], name='NHWC_to_NCHW')
except:
	pass

[<tf.Tensor 'Gs/_Run/concat:0' shape=(?, 3, 512, 512) dtype=float32>]

<tf.Tensor 'Gs/_Run/labels_in:0' shape=<unknown> dtype=float32>: array([], shape=(1, 0), dtype=float64)
<tf.Tensor 'Gs/_Run/latents_in:0' shape=<unknown> dtype=float32>: latents.shape (1, 512)


return tf.nn.conv2d(x, w, strides=[1,1,1,1], padding='SAME', data_format='NCHW')
x = [batch, height, width, in_channels] NHWC ?x512x4x4->?x4x4x512
 x = [batch, height, width, in_channels] NHWC
 w = [height, width, output_channels, input_channels]
 strides = [1, stirde, stride, 1]
 tf.nn.conv2d_transpose(x, w, os, strides=[1,1,2,2], padding='SAME', data_format='NCHW')
 loc:@G_synthesis/4x4/Conv/Noise/weight
weight = tf.get_variable('weight', shape=[x.shape[1].value], initializer=tf.initializers.zeros())
 before and after
 x, weight, noise x.shape[1] -> 4 (?, 4, 4, 512) (4,) (1, 1, 4, 4)
 x.shape[3] -> 4 (?, 512, 4, 4) (4,)  (1, 1, 4, 4)
x = tf.transpose(x, [0,3,1,2], name='NHWC_to_NCHW')