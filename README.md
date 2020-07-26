# MASKRCNN-Setup

MASKRCNN SETUP (MACOS)

Tensorflow Model Garden https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf1_detection_zoo.md

**STEPS FOR CUSTOM TRAINING**
- Download files from below URL into a new folder "MASKRCNN"
  - https://drive.google.com/file/d/1j2a6mvg4dLdm3vTKNRQlTmihc3Xf4hDi/view

- Unzip the files and open the project inside pycharm

- Create a new conda env say "MASKRCNN_tf" in Pycharm project Interpreter Dialog

- Go to terminal and run below command:
  - pip install -r requirements.txt

- Run below command to install object detection API from research folder
  - python setup.py install
  
- Navigate to research folder
  - cd /models/research

- Open create_tf_records.py, right click and run each for train and test folders
  - This will create 2 files: custom_test_dog123.record and custom_train_dog123.record

- On base enviroment run below commands for doing annotation on images:
  - pip install labelme==3.16.7
  - type labelme and press enter
  - github repo: https://github.com/wkentaro/labelme
  - Different annotation tools used in CV
    - labelimg - for object detection to draw square box
    - labelme - for image segementation to draw polygons
    - https://github.com/abreheret/PixelAnnotationTool
    - http://www.robots.ox.ac.uk/~vgg/software/via/
    - CVAT
    
- Create a folder mask_rcnn_training folder inside research folder
  - copy labelmapdog.pbtxt inside mask_rcnn_training folder from /research/object_detection/data folder
  - copy config "mask_rcnn_inception_v2_coco.config" from /research/object_detection/samples/configs/
  - Do following changes in config
    - check fine_tune_checkpoint path as "mask/model.ckpt"
    - num_steps: 10000
    - input_path: "object_detection/data/custom_train_dog123.record" under train_input_reader->tf_record_input_reader
    - label_map_path: "mask_rcnn_training/labelmapdog.pbtxt" under train_input_reader
    - input_path: "object_detection/data/custom_test_dog123.record" under eval_input_reader->tf_record_input_reader
    - label_map_path: "mask_rcnn_training/labelmapdog.pbtxt" under eval_input_reader

- download mask_rcnn_inception_v2_coco model files from below path:
  - https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf1_detection_zoo.md
  - unzip the folder and rename it to just mask and copy the folder inside research folder

- Strat Training by running below command:
  - python train.py --logtostderr --train_dir=mask_rcnn_training/ --pipeline_config_path=mask_rcnn_training/mask_rcnn_inception_v2_coco.config

- Once training stops, run below command for converting ckpt file into pb file
  - python export_inference_graph.py --input_type image_tensor --pipeline_config_path mask_rcnn_training/mask_rcnn_inception_v2_coco.config --trained_checkpoint_prefix mask_rcnn_training/model.ckpt-100 --output_directory inference_graph
  - On running this command "inference_grapgh" folder is created with file "frozen_inference_graph.pb"


