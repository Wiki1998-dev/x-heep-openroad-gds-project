---------------------------------------------Setting up Autotuner-------------------------------------------------------------

sudo apt-get update
sudo apt-get install -y build-essential cmake tcl-dev swig libgsl-dev libfftw3-dev libz-dev libspdlog-dev libeigen3-dev libboost-all-dev libgtest-dev
# Install dependencies
!sudo apt-get update
!sudo apt-get install -y build-essential cmake tcl-dev swig libgsl-dev libfftw3-dev \
                         libz-dev libspdlog-dev libeigen3-dev libboost-all-dev libgtest-dev
%cd /content/S
# Clone OpenROAD-flow-scripts repository
!rm -rf OpenROAD-flow-scripts
!git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git
#!git clone --depth=1 --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git

%cd OpenROAD-flow-scripts

# Modify the OpenROAD version in setup.sh
!sed -i 's|OPENROAD_COMMIT=.*|OPENROAD_COMMIT=d4324e4d31d92314c0ef311b8d3b41cd398763c9|' setup.sh

# Run setup and build OpenROAD locally
!sudo ./setup.sh
!sudo ./build_openroad.sh --local

# Add paths to the environment for the current session
import os
os.environ['PATH'] = f"/content/S/OpenROAD-flow-scripts/tools/install/OpenROAD/bin:" \
                     f"/content/S/OpenROAD-flow-scripts/tools/install/yosys/bin:" + os.environ['PATH']

# Source the environment script and verify the installation
!source ./env.sh
!yosys -help
!openroad -help
--------------------------------------------------------------------------
%cd S/OpenROAD-flow-scripts

# Modify the OpenROAD version in setup.sh
!sed -i 's|OPENROAD_COMMIT=.*|OPENROAD_COMMIT=d4324e4d31d92314c0ef311b8d3b41cd398763c9|' setup.sh

# Run setup and build OpenROAD locally
!sudo ./setup.sh
!sudo ./build_openroad.sh --local



then replace the autotuner and openroad bin files 



---------------------------------------------------------------------------------------------------------------------------------------------

Above combining of two versions is really not required otherwise but in my case it had issues in the flow such that it was not able to continue with latest version 
Adding the project in  use the folder basic and add its files in the designs 
and lib gds and lef 
to see where and how 
https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html


--------------------------------Setting the files for autotuner --------------------------------------------------------------------------------
in folder cd /content/OpenROAD-flow-scripts/flow/util replace the distributed,py as the one there has some issues which were needed to be solved.
You may change the evaluation function in this script and most of other autotuner algorithms

Note: there is script to generate metrics from the intitail opermroad flow to be used for comparing 

--------------------------------------------------Runing --------------------------
cd /content/OpenROAD-flow-scripts/flow/util
import os
os.environ['PLATFORM_DIR'] = '/content/OpenROAD-flow-scripts/flow/platforms/sky130hd'
!python3 distributed.py --design core_v_mini_mcu --platform sky130hd \
                       --config ../designs/sky130hd/core_v_mini_mcu/autotuner.json \
                       tune --algorithm hyperopt --resources_per_trial 2 --eval ppa-improv --reference /content/OpenROAD-flow-scripts/flow/base_metrics.json \
                      --samples 10 --iteration 50
Adjust these above argument based on the requirements 

for reference please see the colab i did few things manually which is not included in the code so ...
https://colab.research.google.com/drive/1vbugGx_zjGAoAc_KpKZXv9uw5suKGVRl?usp=sharing
 i had tried in colab with system as my own pc but its not for sure the one i had used in end 
