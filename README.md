## Compiling tensorflow C API into a static library

**Instructions**

0 - Clone the r1.1 branch into your workspace.

1 - Run ./configure inside the tensorflow folder choosing the default options and not installing anything extra.

2 - Run "tensorflow/contrib/makefile/build_all_ios.sh” from the tensor flow root folder.

The resulting libs will be:
```
"tensorflow/contrib/makefile/gen/lib/libtensorflow-core.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf.a"
"tensorflow/contrib/makefile/gen/protobuf_ios/lib/libprotobuf-lite.a"
```

# Changes to the makefile ** 

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
# Notes
Eventually more operations can be removed from the tf_op_files.txt and tf_proto_files to reduce the final size of the .a library. 

The authors of this makefile already reduce a lot of operations available in the core framework.