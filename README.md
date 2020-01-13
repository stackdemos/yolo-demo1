# Object Detection with YOLOv3
This is an end-to-end pre-integrated machine learning pipeline for object detection.
* Training with [Keras](http://keras.io)
* Real-time object detection with OpenCV and Keras
* Deployment on Kubernetes with [Flask](http://flask.pocoo.org)

Upon successful completion of these stages, user will get a web application that is able to predict
Github issue title by reading it's description.


## Introduction

### Problem
Retrain YOLO3 model to detect new objects. It is based on "keras-yolo3: Training and Detecting Objects with YOLO3"
[keras-yolo3](https://github.com/experiencor/keras-yolo3) by Huynh Ngoc Anh.

Object detection is a computer vision task that involves both localizing one or more objects within an image and classifying each object in the image. This project demonstrates YOLOv3 models, including object detection, transfer learning, and training new models from scratch. We will use a pre-trained model to perform object detection on an unseen photograph.  


### Technologies used:
* Docker registry
* Github
* Jupyter Notebook
* Kubeflow Pipelines
* Keras
* Minio
