# Redefined Relationformer with Improved Matching for Line Detection

## Run baseline model (Relationformer)
**1. SCC Module Requirements:** cuda/12.2 python3/3.9.9 gcc/9.3.0

**2. Build environment**
```sh
git clone https://github.com/chenyu020816/Enhanced-Relationformer-for-Line-Detection.git

# create virtual environment
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```
```sh
cd baseline_models/relatinoformer

# compiling CUDA operators
cd models/ops
python setup.py install
```

**3. Download data**
Option 1: Using gdown
```sh
gdown 1BYXRnSLP1XlbmkMJqwr4BKFjmtPdPO7A -O usgs.zip
gdown 1Kd8TXYPv0LEAXC0NaaeSuKW_l5cBs605 -O uscities.zip
```
Option 2: Download from google drive
usgs data: https://drive.google.com/file/d/1BYXRnSLP1XlbmkMJqwr4BKFjmtPdPO7A/view?usp=sharing
uscities data: https://drive.google.com/file/d/1Kd8TXYPv0LEAXC0NaaeSuKW_l5cBs605/view?usp=drive_link

Unzip the file and move to data folder
```sh
unzip usgs.zip
unzip uscities.zip

mv usgs ./data/
mv 20cities ./data/
```

**4. Data preparation (for uscities data)**
```sh
python generate_data.py
```

**5. Training**
1. Prepare config file
The config file can be found at .configs/road_rgb_2D.yaml. Make custom changes if necessary.
2. Training on multiple-GPU (use ```echo $CUDA_VISIBLE_DEVICES``` to check the index of available GPUs)

Note: The FDR module is still debuging, so the current model is without FDR module.

example:
```sh
python train.py --config configs/{your_config_file} --cuda_visible_device 0 1
```

**Debug**
```sh
error in ms_deformable_im2col_cuda: no kernel image is available for execution on the device
```
If you see this error message when training the model, it is because everytime create a new SCC session, the GPU device might be different, so the CUDA operators need to be recompiled using the new GPU device by following steps.
```sh
cd models/ops

# clean previous compiled operators
python setup.py clean
rm -rf build *.so

# recompile CUDA operators
python setup.py install
```