# Evidential Detection and Tracking Collaboration: New Problem, Benchmark and Algorithm for Robust Anti-UAV System
### Xue-Feng Zhu, Tianyang Xu*, Jian Zhao*, Jia-Wei Liu, Kai Wang, Gang Wang*, Jianan Li*, Qiang Wang, Lei Jin, Zheng Zhu, Junliang Xing, Xiao-Jun Wu*

[[Models and Raw results]](https://drive.google.com/drive/folders/1qp4rZ-d-KnYNLAz80wmqVAl8UXOPGfO-?usp=sharing) (Google Driver)

[[Paper]](https://arxiv.org/pdf/2306.15767.pdf) (ArXiv)

The dataset will be released soon.

## Install the environment
Use the Anaconda
```
conda create -n edtc python=3.6
conda activate edtc
bash install_pytorch17.sh
```


## Training
### Train YOLO
Edit dataset settings: 
```
/path/to/EDTC/yolov5/data/antiuav.yaml
```
Then train the detector:
```
cd /path/to/EDTC/yolov5
python train.py
```

### Train tracking branch
Run the following command to set paths for this project
```
python tracking/create_default_local_file.py --workspace_dir . --data_dir ./data --save_dir .
```
After running this command, you can modify paths by editing the file:
```
lib/train/admin/local.py  # paths about training
```
```
experiments/uavtrack/baseline.yaml # paths about Stage1 training
```
```
experiments/uavtrack_eh/baseline.yaml # paths about Stage2 training
```

Stage1 training:
```
python tracking/train.py --script uavtrack --config baseline --save_dir . --mode multiple --nproc_per_node 8
```
Stage2 training:
```
python tracking/train.py --script uavtrack_eh --config baseline --save_dir /PATH/TO/SAVE/UAVTRACK_EH --mode multiple --nproc_per_node 8 --stage1_model /STAGE1/MODEL/PATH
```

## Evaluation on AntiUAV600
Download the pretrained models [[Models and Raw results]](https://drive.google.com/drive/folders/1qp4rZ-d-KnYNLAz80wmqVAl8UXOPGfO-?usp=sharing) (Google Driver)

Edit the file:
```
lib/test/evaluation/local.py  # paths about testing
./tracking/test.py
```
Edit the Line 133-134 of the files, set them according to the paths of YOLO model:
```
experiments/uavtrack_eh/baseline.yaml  # YOLO pretrained model path
```
Before run the model:
```
export PYTHONPATH=$PYTHONPATH:/path/to/EDTC
export PYTHONPATH=$PYTHONPATH:/path/to/EDTC/yolov5
```

Then run the model:
```
python tracking/test.py uavtrack_eh baseline --dataset antiuav --threads 32 --num_gpus 8 --params__model /path/to/UAVTrackEH.pth.tar --params__search_area_scale 4.55
```

If you want to visualize the tracking results, please set:
```
self.show_result = True   # /path/to/EDTC/lib/test/evaluation/environment.py Line 27 
```


## Evaluation measure
```
python tracking/evaluate_antiuav_performance.py
```
