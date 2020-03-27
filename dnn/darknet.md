# darknet


[darknet](https://pjreddie.com/darknet/)
[darknet.git](https://github.com/pjreddie/darknet.git)
## install


``` bash
git clone https://github.com/pjreddie/darknet
cd darknet 
make
```
darknet可以通过配置依赖cuda和opencv，默认配置不依赖cuda和opencv。


## quickstart

darknet包括多个子命令



快速图片测试
``` bash
./darknet imtest data/eagle.jpg
```



``` bash
# download the pre-trained weight file here (237 MB)
wget https://pjreddie.com/media/files/yolov3.weights
./darknet detect cfg/yolov3.cfg yolo3.weights data/dog.jpg
./darknet detector test cfg/coco.data cfg/yolov3.cfg yolov3.weights data/dog.jpg # 和以上等效
``` 
将会显示一下信息
```
Loading weights from yolov3.weights...Done!
data/dog.jpg: Predicted in 21.730082 seconds.
dog: 100%
truck: 92%
bicycle: 99%
```



``` tree

cfg
data			
example			darknet c demo file
include			c header file
src			c src file
python			python Interface file
scripts			some tools	

```


## interface
``` python
    net = load_net(b"./cfg/yolov3.cfg", b"./yolov3.weights", 0)
    meta = load_meta(b"./cfg/coco.data")
    r = detect(net, meta, b"./data/dog.jpg")
    print (r)
```
## misc

**Q**: 错误：Couldn’t open file: data/coco.names
**A**: 一般是文件编码的错误，由于unix和Windows平台对LF的解释不同导致的。需要使用unix下的压缩包解压。