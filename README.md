# docker-caffe-ssd-py-cuda-cudnn

#### 1、cuda、cudnn、caffe-ssd、opencv3、py2等的安装


```python
# 下载文件
git clone https://github.com/donglin8506/docker-caffe-ssd-py-cuda-cudnn.git
# 进入目录
cd docker-caffe-ssd-py-cuda-cudnn.git
# 创建镜像
sudo nvidia-docker build -t caffe:ssd .  # 其中detect:1.0是构建镜像的名称tag
```
- 构建镜像过程中，出现如下字样，不必担心，这个是提醒python2以后不会再维护（`DEPRECATION`是弃用的意思），不影响镜像构建过程。
```
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. A future version of pip will drop support for Python 2.7. More details about Python 2 support in pip, can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support
```
- 出现该字样，这是跟`setuptools`包的安装有关，因为这个dockerfile是从python3版本更改过来的，可能与python版本有关系。也不影响镜像构建过程。
```python
ERROR: Package 'setuptools' requires a different Python: 2.7.12 not in '>=3.5'
```

可以通过下列方式解决

```
pip install --upgrade 'setuptools<45.0.0'
```

- 出现该警告信息，也不必担心。
```
In file included from src/caffe/util/math_functions.cu:1:0:
/usr/local/cuda/include/math_functions.h:54:2: warning: #warning "math_functions.h is an internal header file and must not be used directly.  This file will be removed in a future CUDA release.  Please use cuda_runtime_api.h or cuda_runtime.h instead." [-Wcpp]
```

构建成功后，最后会出现这样的日志：
```
...

Removing intermediate container a8072cff5dfb
 ---> cf00fa49b6e1
Step 18/19 : ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
 ---> Running in b87a6a729b46
Removing intermediate container b87a6a729b46
 ---> 1159d7e0a3d1
Step 19/19 : RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
 ---> Running in dc4351ef8b79
Removing intermediate container dc4351ef8b79
 ---> 3dd16711ac88
Successfully built 3dd16711ac88
Successfully tagged caffe:ssd
```
#### 2、其他一些报错

1） protobuf错误
```
>>> import caffe
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/caffe/python/caffe/__init__.py", line 1, in <module>
    from .pycaffe import Net, SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver, AdamSolver
  File "/opt/caffe/python/caffe/pycaffe.py", line 15, in <module>
    import caffe.io
  File "/opt/caffe/python/caffe/io.py", line 8, in <module>
    from caffe.proto import caffe_pb2
  File "/opt/caffe/python/caffe/proto/caffe_pb2.py", line 6, in <module>
    from google.protobuf.internal import enum_type_wrapper
ImportError: No module named google.protobuf.internal
```
解决办法：
```
pip install protobuf
```

2） skimage.io错误
```python
>>> import caffe
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/caffe/python/caffe/__init__.py", line 1, in <module>
    from .pycaffe import Net, SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver, AdamSolver
  File "/opt/caffe/python/caffe/pycaffe.py", line 15, in <module>
    import caffe.io
  File "/opt/caffe/python/caffe/io.py", line 2, in <module>
    import skimage.io
ImportError: No module named skimage.io
```
解决办法:
```
pip install scikit-image
```
如果在安装过程中，出现setuptools的错误，则使用命令:
```
pip install --upgrade 'setuptools<45.0.0'
pip install scikit-image
```

3）fatal error: caffe/proto/caffe.pb.h: No such file or directory #include "caffe/proto/caffe.pb.h

在caffe的安装目录下运行下面命令：

```
protoc src/caffe/proto/caffe.proto --cpp_out=.

mkdir include/caffe/proto

mv src/caffe/proto/caffe.pb.h include/caffe/proto
```


