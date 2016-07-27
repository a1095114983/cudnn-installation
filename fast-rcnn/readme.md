#https://github.com/rbgirshick/py-faster-rcnn

Q1: error: utils/bbox.c: No such file or directory
    x86_64-linux-gnu-gcc: error: utils/bbox.c: No such file or directory
    x86_64-linux-gnu-gcc: fatal error: no input files
    compilation terminated.
    error: command ‘x86_64-linux-gnu-gcc’ failed with exit status 4
    make: * [all] Error 1

A1: 手动用python生成bbox.c

cd $FRCN_ROOT/lib/utils
cython bbox.pyx


Q2: error: nms/cpu_nms.c: No such file or directory
    x86_64-linux-gnu-gcc: error: nms/cpu_nms.c: No such file or directory
    x86_64-linux-gnu-gcc: fatal error: no input files
    compilation terminated.
    error: command ‘x86_64-linux-gnu-gcc’ failed with exit status 4
    make: * [all] Error 1

A2: 手动用python生成cpu_nms.c

cd $FRCN_ROOT/lib/nms
cython cpu_nms.pyx



Q3: error: nms/gpu_nms.c: No such file or directory
    x86_64-linux-gnu-gcc: error: nms/gpu_nms.c: No such file or directory
    x86_64-linux-gnu-gcc: fatal error: no input files
    compilation terminated.
    error: command ‘x86_64-linux-gnu-gcc’ failed with exit status 4
    make: * [all] Error 1

A3: 手动用cython生成gpu_nms.c

cd $FRCN_ROOT/lib/nms
cython gpu_nms.pyx



Q4: Makefile.config not found
    Makefile:6: * Makefile.config not found. See Makefile.config.example.. Stop.

A4: $FRCN_ROOT/caffe-fast-rcnn目录下，作者提供了Makefile.config.example

cd $FRCN_ROOT/caffe-fast-rcnn
cp Makefile.config.example Makefile.config



Q5: undefined symbol: _nms

    当在$FRCN_ROOT/lib下make时出现 

    Traceback (most recent call last):
    File “./demo.py”, line 18, in module
    from fast_rcnn.test import im_detect
    File “/home/lijiajun/py-faster-rcnn-blog/tools/../lib/fast_rcnn/test.py”, line 17, in module
    from fast_rcnn.nms_wrapper import nms
    File “/home/lijiajun/py-faster-rcnn-blog/tools/../lib/fast_rcnn/nms_wrapper.py”, line 9, in module
    from nms.gpu_nms import gpu_nms
    ImportError: /home/lijiajun/py-faster-rcnn-blog/tools/../lib/nms/gpu_nms.so: undefined symbol: _nms

A5: gpu_nms.so编译时有误

编辑setup.py

cd $FRCN_ROOT/lib
vim setup.py



将gpu_nms.pyx改为gpu_nms.cpp

Extension('nms.gpu_nms',
['nms/nms_kernel.cu', 'nms/gpu_nms.pyx'],
...



Extension('nms.gpu_nms',
['nms/nms_kernel.cu', 'nms/gpu_nms.cpp'],
...



修改gpu_nms.c文件后缀为.cpp

cd $FRCN_ROOT/lib/nms
mv gpu_nms.c gpu_nms.cpp
rm gpu_nms.so



Q6: Check failed: registry.count(type) == 1 (0 vs. 1) Unknown layer type
    I1221 19:43:06.790405 12895 layer_factory.hpp:76] Creating layer proposal
    F1221 19:43:06.790431 12895 layer_factory.hpp:80] Check failed: registry.count(type) == 1 (0 vs. 1) Unknown layer type: Python (known types: AbsVal, Accuracy, ArgMax, BNLL, Concat, ContrastiveLoss, Convolution, Data, Deconvolution, Dropout, DummyData, Eltwise, Embed, EuclideanLoss, Exp, Filter, Flatten, HDF5Data, HDF5Output, HingeLoss, Im2col, ImageData, InfogainLoss, InnerProduct, LRN, Log, MVN, MemoryData, MultinomialLogisticLoss, PReLU, Pooling, Power, ROIPooling, ReLU, Reduction, Reshape, SPP, Sigmoid, SigmoidCrossEntropyLoss, Silence, Slice, SmoothL1Loss, Softmax, SoftmaxWithLoss, Split, TanH, Threshold, Tile, WindowData)
    *** Check failure stack trace: ***
    Aborted (core dumped)

A6: caffe的Makefile.config配置有误

    Note: Caffe must be built with support for Python layers!

# In your Makefile.config, make sure to have this line uncommented
WITH_PYTHON_LAYER := 1



修改完成后

cd $FRCN_ROOT/caffe-fast-rcnn
make clean
make -j
make pycaffe



Q7: ImportError: No module named _caffe
    Traceback (most recent call last):
    File “./demo.py”, line 18, in < module>
    from fast_rcnn.test import im_detect
    File “/home/lijiajun/py-faster-rcnn-blog/tools/../lib/fast_rcnn/test.py”, line 16, in < module>
    import caffe
    File “/home/lijiajun/py-faster-rcnn-blog/tools/../caffe-fast-rcnn/python/caffe/init.py”, line 1, in < module>
    from .pycaffe import Net, SGDSolver
    File “/home/lijiajun/py-faster-rcnn-blog/tools/../caffe-fast-rcnn/python/caffe/pycaffe.py”, line 13, in < module>
    from ._caffe import Net, SGDSolver
    ImportError: No module named _caffe

A7: 没有编译pycaffe

cd $FRCN_ROOT/caffe-fast-rcnn
make pycaffe



Q8: tkinter.TclError: no display name and no $DISPLAY environment variable
    Traceback (most recent call last):
    File “./demo.py”, line 149, in < module>
    demo(net, im_name)
    File “./demo.py”, line 98, in demo
    vis_detections(im, cls, dets, thresh=CONF_THRESH)
    File “./demo.py”, line 47, in vis_detections
    fig, ax = plt.subplots(figsize=(12, 12))
    File “/usr/lib/pymodules/python2.7/matplotlib/pyplot.py”, line 1046, in subplots
    fig = figure(**fig_kw)
    File “/usr/lib/pymodules/python2.7/matplotlib/pyplot.py”, line 423, in figure**kwargs)
    File “/usr/lib/pymodules/python2.7/matplotlib/backends/backend_tkagg.py”, line 79, in new_figure_manager
    return new_figure_manager_given_figure(num, figure)
    File “/usr/lib/pymodules/python2.7/matplotlib/backends/backend_tkagg.py”, line 87, in new_figure_manager_given_figure
    window = Tk.Tk()
    File “/usr/lib/python2.7/lib-tk/Tkinter.py”, line 1767, in __init__
    self.tk = _tkinter.create(screenName, baseName, className, interactive, wantobjects, useTk, sync, use)
    _tkinter.TclError: no display name and no $DISPLAY environment variable

A8: 缺少可视化环境引起的

切换到图形界面后在执行脚本。

或者，修改demo.py，将调用函数vis_detections()的代码注释。

Q9: ImportError: No module named nms

A9: 重新进去libmake

Q10： ImportError: No module named yaml

A10： apt-get install yaml
