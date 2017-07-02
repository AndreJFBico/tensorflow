TensorFlow compiling C api for ios.

## Compiling tensorflow C API and C++ core into a static library for iOS

**Tools/libs required**
You need bazel and its dependencies.
https://docs.bazel.build/versions/master/install.html

**Instructions**
0 - Clone this project into your Bazel Workspace folder.
1 - Run ./configure inside the tensorflow folder choosing the default options and not installing anything extra.
2 - Run the following command from the tensorflow root folder
(Note it will take quite some time to finish):
"bazel build --config opt //tensorflow/tools/lib_package:libtensorflow"
**-**
This will compile the C api together with the C++ core framework. Additionally it will also transform any .proto files and required operations into .cc and .h files which we will need. These will present in the "tensorflow/bazel-genfiles" folder.
**--**
3 - Run "tensorflow/contrib/makefile/build_all_ios.sh” from the tensor flow root folder.

The resulting libs:
"tensorflow/contrib/makefile/gen/lib/libtensorflow-core.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf-lite.a"

**Changes in this fork related to the 1.2.1 tensor flow source version** 
Just listed the files i changed in this fork of tensorflow.
/Users/Andre/BazelWorkSpace/TensorFlowForked/tensorflow/contrib/makefile/Makefile
/Users/Andre/BazelWorkSpace/TensorFlowForked/tensorflow/contrib/makefile/tf_op_files.txt

# Changes to the makefile
Added the c_api.cc file which defines the C API and its dependencies, and the .c and .h files from the bazel-genfiles folder.
```
TF_BAZEL_GEN_SRCS :=\
$(wildcard bazel-genfiles/tensorflow/core/*.cc) \
$(wildcard bazel-genfiles/tensorflow/core/framework/*.cc) \
$(wildcard bazel-genfiles/tensorflow/core/grappler/*.cc) \
$(wildcard bazel-genfiles/tensorflow/core/grappler/*/*.cc) \
$(wildcard bazel-genfiles/tensorflow/core/lib/*/*.cc) \
$(wildcard bazel-genfiles/tensorflow/core/util/*.cc) \
$(wildcard bazel-genfiles/tensorflow/cc/ops/*.cc)
```

```
TF_I_SRCS_EXCLUDE := \
$(wildcard tensorflow/cc/ops/const_op_test.cc) 
```

```
TF_I_SRCS := \
$(wildcard tensorflow/c/c_api.cc) \
$(wildcard tensorflow/c/checkpoint_reader.cc) \
$(wildcard tensorflow/c/tf_status_helper.cc) \
$(wildcard tensorflow/cc/saved_model/loader.cc) \
$(wildcard tensorflow/cc/framework/scope.cc) \
$(wildcard tensorflow/cc/framework/ops.cc) \
$(wildcard tensorflow/cc/framework/gradients.cc) \
$(wildcard tensorflow/cc/framework/grad_op_registry.cc) \
$(wildcard tensorflow/cc/ops/*.cc) 
TF_I_SRCS := $(filter-out $(TF_I_SRCS_EXCLUDE), $(TF_I_SRCS))
```

```
TF_CCC_OBJS := $(addprefix $(OBJDIR), $(TF_I_SRCS:.cc=.o))
```

```
LIB_OBJS := $(PROTO_OBJS) $(TF_CC_OBJS) $(TF_CCC_OBJS) $(PBT_OBJS)
```
```
.PHONY: rsync -rupE bazel-genfiles/ .
```
# Changes to the tf_op_files.txt
Removed
```
tensorflow/contrib/boosted_trees/ops/ensemble_optimizer_ops.cc
tensorflow/contrib/boosted_trees/ops/model_ops.cc
tensorflow/contrib/boosted_trees/ops/prediction_ops.cc
tensorflow/contrib/boosted_trees/ops/quantile_ops.cc
tensorflow/contrib/boosted_trees/ops/split_handler_ops.cc
tensorflow/contrib/boosted_trees/ops/stats_accumulator_ops.cc
tensorflow/contrib/boosted_trees/ops/training_ops.cc
```
Compiled and ran with a previous tensorflow version that did not need these operations.

# Changes to the tf_proto_files.txt
Removed
```
tensorflow/contrib/boosted_trees/proto/learner.proto
tensorflow/contrib/boosted_trees/proto/quantiles.proto
tensorflow/contrib/boosted_trees/proto/split_info.proto
tensorflow/contrib/boosted_trees/proto/tree_config.proto
```
Added
```
tensorflow/core/protobuf/saved_model.proto
```



