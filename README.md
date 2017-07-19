## Compiling tensorflow C API into a static library

**Instructions**

0 - Clone this project into your workspace.

1 - Make sure you have bazel installed and that you can run it on the command line.

2 - Run ./configure inside the tensorflow folder choosing the default options and not installing anything extra.

3 - Run "tensorflow/contrib/makefile/build_all_ios.sh” from the tensor flow root folder.

The resulting libs will be:
```
"tensorflow/contrib/makefile/gen/lib/libtensorflow-core.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf-lite.a"
```

# Changes to the makefile

Added the c_api.cc file which defines the C API and its dependencies to the standard compilation flow.

```
TF_I_SRCS_EXCLUDE := \
$(wildcard tensorflow/cc/ops/const_op_test.cc) 
```

```
TF_I_SRCS := \
$(wildcard tensorflow/c/c_api.cc) \
$(wildcard tensorflow/c/checkpoint_reader.cc) \
$(wildcard tensorflow/c/tf_status_helper.cc) \
$(wildcard tensorflow/cc/saved_model/loader.cc) 
TF_I_SRCS := $(filter-out $(TF_I_SRCS_EXCLUDE), $(TF_I_SRCS))
```

```
TF_CCC_OBJS := $(addprefix $(OBJDIR), $(TF_I_SRCS:.cc=.o))
```

```
LIB_OBJS := $(PROTO_OBJS) $(TF_CC_OBJS) $(TF_CCC_OBJS) $(PBT_OBJS)
```
# Changes to the tf_proto_files.txt
Added
```
tensorflow/core/protobuf/saved_model.proto
```

# Changes to the tensorflow source
I had to remove the following operation from c_api.h and c_api.cc as it requires other code that doesn't seem to link properly for iOS. 

Note: The original file also excluded this operation for android.
```
TF_AddGradients
```

# Notes
Eventually more operations can be removed/added from the tf_op_files.txt and tf_proto_files depending on what is required.
