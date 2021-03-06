# LiteFlowNet
This repository is the release of <strong>LiteFlowNet</strong> for our paper <a href="https://arxiv.org/pdf/1805.07036.pdf"><strong>LiteFlowNet: A Lightweight Convolutional Neural Network for Optical Flow Estimation</strong></a> in CVPR18 (Spotlight). <i>The updated results are presented in my <a href="https://arxiv.org/pdf/1805.07036.pdf"><strong>arXiv paper</strong></a></i>. 

For more details about LiteFlowNet, please visit <a href="http://mmlab.ie.cuhk.edu.hk/projects/LiteFlowNet/"><strong>my project page</strong></a>.

It comes as a fork of modified caffe master branches from <a href="https://lmb.informatik.uni-freiburg.de/resources/software.php">DispFlowNet</a> and <a href="https://github.com/lmb-freiburg/flownet2">FlowNet2</a> with our new layers, scripts, and trained models.

# License and Citation 
All code is provided for research purposes only and without any warranty. Any commercial use requires our consent. When using any parts of the code package or the paper (<i>LiteFlowNet: A Lightweight Convolutional Neural Network for Optical Flow Estimation</i>) in your work, please cite the following paper:

<pre><code>@InProceedings{hui18liteflownet,    
 author = {Tak-Wai Hui and Xiaoou Tang and Chen Change Loy},    
 title = {LiteFlowNet: A Lightweight Convolutional Neural Network for Optical Flow Estimation},    
 booktitle  = {Proceedings of IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},    
 year = {2018},    
 url = {http://mmlab.ie.cuhk.edu.hk/projects/LiteFlowNet/} 
}</code></pre>

# Prerequisites
Installation was tested under Ubuntu 14.04.5 and 16.04.2 with CUDA 8.0 and cuDNN 5.1. 

For opencv 3+, you may need to change <code>opencv2/gpu/gpu.hpp</code> to <code>opencv2/cudaarithm.hpp</code> in <code>/LiteFlowNet/src/caffe/layersresample_layer.cu</code>.

If your machine installed a newer version of cuDNN, you do not need to downgrade it. You can do the following trick: 
1. Download <code>cudnn-8.0-linux-x64-v5.1.tgz</code> and untar it to a temp folder, say <code>cuda-8-cudnn-5.1</code>	

2. Rename <code>cudnn.h</code> to <code>cudnn-5.1.h</code> in the folder <code>/cuda-8-cudnn-5.1/include</code>	

3. <pre><code>$ sudo cp cuda-8-cudnn-5.1/include/cudnn-5.1.h /usr/local/cuda/include/</code>	
   <code>$ sudo cp cuda-8-cudnn-5.1/lib64/lib* /usr/local/cuda/lib64/</code></pre>	

4. Replace <code>#include <cudnn.h></code> to <code>#include <cudnn-5.1.h></code> in <code>LiteFlowNet/include/caffe/util/cudnn.hpp</code>.
    
# Compiling
<pre><code>$ cd LiteFlowNet</code>
<code>$ make -j 8 all tools pycaffe</code></pre>

# Datasets
1. <a href="https://lmb.informatik.uni-freiburg.de/data/FlyingChairs/FlyingChairs.zip"> FlyingChairs dataset</a> (31GB) and <a href="https://lmb.informatik.uni-freiburg.de/resources/datasets/FlyingChairs/FlyingChairs_train_val.txt">train-validation split</a>.
2. <a href="https://lmb.informatik.uni-freiburg.de/data/SceneFlowDatasets_CVPR16/Release_april16/data/FlyingThings3D/raw_data/flyingthings3d__frames_cleanpass.tar"> RGB image pairs (clean pass)</a> (37GB) and <a href="https://lmb.informatik.uni-freiburg.de/data/SceneFlowDatasets_CVPR16/Release_april16/data/FlyingThings3D/derived_data/flyingthings3d__optical_flow.tar.bz2"> flow fields</a> (311GB) for Things3D dataset.
3. <a href="http://files.is.tue.mpg.de/sintel/MPI-Sintel-complete.zip"> Sintel dataset (clean + final passes)</a> (5.3GB).
4. <a href="http://www.cvlibs.net/download.php?file=data_stereo_flow.zip"> KITTI12 dataset</a> (2GB) and <a href="http://www.cvlibs.net/download.php?file=data_scene_flow.zip"> KITTI15 dataset</a> (2GB) (Simple registration is required).

</ul>
<table>
<thead>
<tr>
<th align="center"></th>
<th align="center">FlyingChairs</th>
<th align="center">FlyingThings3D</th>
<th align="center">Sintel</th>
<th align="center">KITTI</th>
</tr>
<tr>
<td align="center">Crop size</td>
<td align="center">448 x 320</td>
<td align="center">768 x 384</td>
<td align="center">768 x 384</td>
<td align="center">896 x 320</td>
</tr>  
<tr>
<td align="center">Batch size</td>
<td align="center">8</td>
<td align="center">4</td>
<td align="center">4</td>
<td align="center">4</td>
</tr>    
</tbody></table>

# Training
1. Prepare the training set. In <code>LiteFlowNet/data/make-lmdbs-train.sh</code>, change <code>YOUR_TRAINING_SET</code> and <code>YOUR_TESTING_SET</code> to your favourite dataset.
<pre><code>$ cd LiteFlowNet/data</code>
<code>$ ./make-lmdbs-train.sh</code></pre>

2. Copy files from <code>LiteFlowNet/models/training_template</code> to a new model folder (e.g. <code>NEW</code>). Edit all the files and make sure all settings are correct.
<pre><code>$ mkdir LiteFlowNet/models/NEW</code>
<code>$ cd LiteFlowNet/models/NEW</code>
<code>$ cp ../training_template/solver.prototxt.template solver.prototxt</code>	
<code>$ cp ../training_template/train.prototxt.template train.prototxt</code>
<code>$ cp ../training_template/train.py.template train.py</code></pre>		

3. Create a soft link in your new model folder
<pre><code>$ ln -s ../../build/tools bin</code></pre>

4. Run the training script	
<pre><code>$ ./train.py -gpu 0 2>&1 | tee ./log.txt</code></pre>

# Trained models	
The trained models (<code>liteflownet</code>, <code>liteflownet-ft-sintel</code>, <code>liteflownet-ft-kitti</code>) are available in the folder <code>LiteFlowNet/models/trained</code>. Untar the files to the same folder before you use it.

# Testing
1. Open the testing folder
<pre><code>$ cd LiteFlowNet/models/testing</pre></code>

2. Create a soft link in the folder <code>/testing</code>
<pre><code>$ ln -s ../../build/tools bin</code></pre>

3. Replace <code>MODE</code> in <code>./test_MODE.py</code> to <code>batch</code> if all the images has the same resolution (e.g. Sintel dataset), otherwise replace it to <code>iter</code> (e.g. KITTI dataset).

4. Replace <code>MODEL</code> in line 10 (<code>cnn_model = 'MODEL'</code>) of <code>test_MODE.py</code> to one of the trained models (e.g. <code>liteflownet-ft-sintel</code>).

5. Run the testing script. Flow fields (<code>MODEL</code>-0000000.flo, <code>MODEL</code>-0000001.flo, ... etc) are stored in the folder <code>/testing/results</code> having the same order as the image pair sequence. 
<pre><code>$ test_MODE.py img1_pathList.txt img2_pathList.txt results</code></pre>

# Evaluation
1. End-point error (EPE) per image can be calculated using the provided script <code>LiteFlowNet/models/testing/util/endPointErr.m</code>

2. Average end-point error (AEE) is simply computed by taking the average of all EPE.
