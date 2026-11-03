
## Installing Pufferlib on the RWTH Cluster (CLAIX)

To start, ssh into `login23-g-1.hpc.itc.rwth-aachen.de` which is the only login node with GPUs.

#### Installing ccache (dependency of pufferlib).

We install it into the `~/.local/bin` folder

    git clone https://github.com/ccache/ccache.git
    cd ccache
    mkdir build
    cd build
    cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=~/.local ..
    make -j
    make install
    
To make `ccache` findable from your shell, you need to add the `~/.local/bin` folder to your PATH. Simply put this in the `~/.zshrc` file:

    export PATH="${PATH}:$HOME/.local/bin"
    
Then source the `~/.zshrc` file to reload it manually:
    
    `source ~/.zshrc`
    
#### Getting Pufferlib
    
    cd
    git clone https://github.com/PufferAI/PufferLib.git
    
## Training with Pufferlib on the cluster

Load CUDA modules:

    module purge
    module load GCCcore/12.3.0
    module load CUDA/12.3.0
    module load NCCL/2.20.5-CUDA-12.3.0
    
Building and training the drone example:

    cd PufferLib
    ./setup drone
    ./puffer train
    
## Local evaluation

Synchonize the checkpoints (trained models) between the cluster and your local pufferlib:  
Replace `cluster` with your actual ssh alias or `username@login23-g-1.hpc.itc.rwth-aachen.de`.  

    cd pufferlib
    rsync -avP cluster:~/PufferLib/checkpoints/ ./checkpoints/
    
Compile and run the drone environment locally, using the latest checkpoint.

    ./build.sh drone --cpu
    ./drone eval latest
