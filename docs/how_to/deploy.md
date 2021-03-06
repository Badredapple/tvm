How to Deploy TVM Modules
=========================
We provide an example on how to deploy TVM modules in [apps/howto_deploy](https://github.com/dmlc/tvm/tree/master/apps/howto_deploy)

To run the example, you can use the following command

```bash
cd apps/howto_deploy
./run_example.sh
```

Get TVM Runtime Library
-----------------------

![](http://www.tvmlang.org/images/release/tvm_flexible.png)

The only thing we need is to link to a TVM runtime in your target platform.
TVM provides a minimum runtime, which costs around 300K to 600K depending on how much modules we use.
In most cases, we can use ```libtvm_runtime.so``` that comes with the build.

If somehow you find it is hard to build ```libtvm_runtime```, checkout [tvm_runtime_pack.cc](https://github.com/dmlc/tvm/tree/master/apps/howto_deploy/tvm_runtime_pack.cc).
It is an example all in one file that gives you TVM runtime.
You can compile this file using your build system and include this into your project.

You can also checkout [apps](https://github.com/dmlc/tvm/tree/master/apps/) for example applications build with TVM on iOS, Android and others.

Dynamic Library vs. System Module
---------------------------------
TVM provides two ways to use the compiled library.
You can checkout [prepare_test_libs.py](https://github.com/dmlc/tvm/tree/master/apps/howto_deploy/prepare_test_libs.py)
on how to generate the library and [cpp_deploy.cc](https://github.com/dmlc/tvm/tree/master/apps/howto_deploy/cpp_deploy.cc) on how to use them.

- Store library as a shared library and dynamically load the library into your project.
- Bundle the compiled library into your project in system module mode.

Dynamic loading is more flexible and can load new modules on the fly. System module is a more ```static``` approach.  We can use system module in places where dynamic library loading is banned.

Deploy to Android
-----------------
### Build model for Android Target

NNVM compilation of model for android target could follow same approach like android_rpc.

An reference exampe can be found at [chainer-nnvm-example](https://github.com/tkat0/chainer-nnvm-example)

Above example will directly run the compiled model on RPC target. Below modification at [rum_mobile.py](https://github.com/tkat0/chainer-nnvm-example/blob/5b97fd4d41aa4dde4b0aceb0be311054fb5de451/run_mobile.py#L64) will save the compilation output which is required on android target.

```
lib.export_library("deploy_lib.so", ndk.create_shared)
with open("deploy_graph.json", "w") as fo:
    fo.write(graph.json())
with open("deploy_param.params", "wb") as fo:
    fo.write(nnvm.compiler.save_param_dict(params))
```

deploy_lib.so, deploy_graph.json, deploy_param.params will go to android target.

### TVM Runtime for Android Target

Refer [here](https://github.com/dmlc/tvm/blob/master/apps/android_deploy/README.md#build-and-installation) to build CPU/OpenCL version flavor TVM runtime for android target.
From android java TVM API to load model & execute can be refered at this [java](https://github.com/dmlc/tvm/blob/master/apps/android_deploy/app/src/main/java/ml/dmlc/tvm/android/demo/MainActivity.java) sample source.
