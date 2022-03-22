# AI DAN BIG DATA ANALYSIS
Mata Kuliah Pilihan AI DAN BIG DATA ANALYSIS Telkom University

## Extra Project - Yolo v2 Mask Detection
[Extra Project Use YoloV2](https://github.com/anggaraputrapratama/YoloV2-MaskDetection)<br>
Project Team : <br>
1. [Anggara Putra Pratama - 1101174240](https://github.com/anggaraputrapratama)<br>
2. [Regina Acintya P.M. - 1101174141](https://github.com/rererrw) <br>
3. M. Kemal Hernandi - 1101174169<br>


![Test Images YoloV2](https://raw.githubusercontent.com/anggaraputrapratama/AI-BigData/main/test_image-Yolov2.png)


## How to train (to detect your custom objects):
1. Create file `yolov2-custom.cfg` with the same content as in `yolo-voc.2.0.cfg` (or copy `yolo-voc.2.0.cfg` to `yolov2-custom.cfg)` and:

  * change line batch to [`batch=64`](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/yolo-voc.2.0.cfg#L2)
  * change line subdivisions to [`subdivisions=8`](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/yolo-voc.2.0.cfg#L3)
  * change line `classes=20` to your number of objects
  * change line #237 from [`filters=125`](https://github.com/AlexeyAB/darknet/blob/master/cfg/yolo-voc.2.0.cfg#L224) to: filters=(classes + 5)x5, so if `classes=2` then should be `filters=35`. Or if you use `classes=3` then write `filters=40`, **do not write in the cfg-file: filters=(classes + 5)x5**.
  
  (Generally `filters` depends on the `classes`, `num` and `coords`, i.e. equal to `(classes + coords + 1)*num`, where `num` is number of anchors)

  So for example, for 2 objects, your file `yolo-obj.cfg` should differ from `yolo-voc.2.0.cfg` in such lines:

  ```
  [convolutional]
  filters=35

  [region]
  classes=2
  ```

2. Create file `obj.names` in the directory `build\darknet\x64\data\`, with objects names - each in new line

3. Create file `obj.data` in the directory `build\darknet\x64\data\`, containing (where **classes = number of objects**):

  ```
  classes= 2
  train  = data/train.txt
  valid  = data/test.txt
  names = data/obj.names
  backup = backup/
  ```

4. Put image-files (.jpg) of your objects in the directory `build\darknet\x64\data\obj\`

5. Create `.txt`-file for each `.jpg`-image-file - in the same directory and with the same name, but with `.txt`-extension, and put to file: object number and object coordinates on this image, for each object in new line: `<object-class> <x> <y> <width> <height>`

  Where: 
  * `<object-class>` - integer number of object from `0` to `(classes-1)`
  * `<x> <y> <width> <height>` - float values relative to width and height of image, it can be equal from 0.0 to 1.0 
  * for example: `<x> = <absolute_x> / <image_width>` or `<height> = <absolute_height> / <image_height>`
  * atention: `<x> <y>` - are center of rectangle (are not top-left corner)

  For example for `img1.jpg` you should create `img1.txt` containing:

  ```
  1 0.716797 0.395833 0.216406 0.147222
  0 0.687109 0.379167 0.255469 0.158333
  1 0.420312 0.395833 0.140625 0.166667
  ```

6. Create file `train.txt` in directory `build\darknet\x64\data\`, with filenames of your images, each filename in new line, with path relative to `darknet.exe`, for example containing:

  ```
  data/obj/img1.jpg
  data/obj/img2.jpg
  data/obj/img3.jpg
  ```

7. Download pre-trained weights for the convolutional layers (76 MB): http://pjreddie.com/media/files/darknet19_448.conv.23 and put to the directory `build\darknet\x64`

8. Start training by using the command line: `darknet.exe detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23`

    (file `yolo-obj_xxx.weights` will be saved to the `build\darknet\x64\backup\` for each 100 iterations)
    (To disable Loss-Window use `darknet.exe detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23 -dont_show`, if you train on computer without monitor like a cloud Amazaon EC2)

9. After training is complete - get result `yolo-obj_final.weights` from path `build\darknet\x64\backup\`

 * After each 1000 iterations you can stop and later start training from this point. For example, after 2000 iterations you can stop training, and later just copy `yolo-obj_2000.weights` from `build\darknet\x64\backup\` to `build\darknet\x64\` and start training using: `darknet.exe detector train data/obj.data yolo-obj.cfg yolo-obj_2000.weights`

 * Also you can get result earlier than all 45000 iterations.
 
### How to train tiny-yolo (to detect your custom objects):

Do all the same steps as for the full yolo model as described above. With the exception of:
* Download default weights file for tiny-yolo-voc: http://pjreddie.com/media/files/tiny-yolo-voc.weights
* Get pre-trained weights tiny-yolo-voc.conv.13 using command: `darknet.exe partial cfg/tiny-yolo-voc.cfg tiny-yolo-voc.weights tiny-yolo-voc.conv.13 13`
* Make your custom model `tiny-yolo-obj.cfg` based on `tiny-yolo-voc.cfg` instead of `yolo-voc.2.0.cfg`
* Start training: `darknet.exe detector train data/obj.data tiny-yolo-obj.cfg tiny-yolo-voc.conv.13`

For training Yolo based on other models ([DenseNet201-Yolo](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/densenet201_yolo.cfg) or [ResNet50-Yolo](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/resnet50_yolo.cfg)), you can download and get pre-trained weights as showed in this file: https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/partial.cmd
If you made you custom model that isn't based on other models, then you can train it without pre-trained weights, then will be used random initial weights.
