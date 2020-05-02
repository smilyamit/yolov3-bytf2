# yolov3-bytf2

* OpenCV is the computer vision library/ framework that we we will be using to support our YOLOv3 algorithm

*OpenCV has inbuilt support for Darknet Architecture.

*Darknet Architecture is pre-trained model for classifying 80 different classes. Our goal now is that we will use Darknet(YOLOv3) in OpenCV to classify objects using Python language.
*YOLO is an object detection algorithm (Check out the paper came out it 2015 here). The recent YOLOv3 is more powerful than basic YOLO and YOLOv2

*We need 3 main files
yolo.cfg (Download from here) — Configuration file
yolo.weights (Download from here) — pre-trained weights
coco.names (Download from here)- 80 classes names

*First step is to import cv2 and numpy libraries
*Then we load yolo v3 algorithm using cv2.dnn.readNet by passing weights and cfg file

* Then we will load all classes names in array using coco.names file.

*Next we will define output layers because that’s where we will be defining what object is detected by using net.getUnconnectedOutLayers and net.getLayerNames

*Next let us load an image. We will reduce the height and width of our image to scale of 40% and 30%. And save all those values in height,width,channels variables for theoriginal image.


*But we cannot give this image directly to algorithm.So we need to do some conversion from this image. This is called blob conversion which is basically extracting features from image.
*Blob is a library for computer vision to detect connected regions in binary digital images

*We will detect objects in blob by using cv2.dnn.blobFromImage and passing few variables
*blob = cv2.dnn.blobFromImage(img,0.00392,(416,416),(0,0,0),True,crop=False)

=>Here img is file name, scalefactor of 0.00392, size of image to be used in blob be (416,416), no mean subtraction from       layers as (0,0,0), setting True flag means we will be inverting blue with red since OpenCV uses BGR but real img is rgb
*note: scale is not actually resizing the input image but converting each element of the image to a float in the [0.0, 1.0] range 0.00392 is approximately 1/255.

*We now pass this blob to network using net.setInput(blob) and then forward this to the outputlayers. Here all objects have been detected and outs contains all the information we need to instruct to extract the position of the object like top,left,right,bottom positions,name of class
i.e: net.setInput(blob)
outs = net.forward(outputlayers)

*Now lets evaluate outs by showing information on screen. Mainly we will be trying to predict the confidence meaning how confident algorithm is when it predict some object. For this, we will loop through outs, first get all the scores for each out in outs. Then get the class_id that has highest score amongst them and then assign confidence to value of scores by passing class_id.

*Now we will assign the confidence level threshold as 0.5. Anything above 0.5 should mean object detected
*Let also have the center_x,center_y, w as width, h as height of the object detected. Here we will use height,width variables we saved previously of original image

*Further lets draw rectangle around detected object by using center_x,center_y,w,h. And append some information to that like class,confidence.

*There might be cases that multiple time the same object might be detected
*To eliminate this, we will use Non-Max Suppression(NMS) functionality. What this will do is eliminate the boxes by using some threshold value(any box having value less than 0.6- that will be removed
indexes = cv2.dnn.NMSBoxes(boxes,confidences,0.4,0.6)

*Now using below loop over all found boxes, if box is appearing in indexes then only draw rectangle, color it, put text of class name on it.
*font = cv2.FONT_HERSHEY_PLAIN
for i in range(len(boxes)):
    if i in indexes:
        x,y,w,h = boxes[i]
        label = str(classes[class_ids[i]])
        color = colors[i]
        cv2.rectangle(img,(x,y),(x+w,y+h),color,2)
        cv2.putText(img,label,(x,y+30),font,1,(255,255,255),2)
