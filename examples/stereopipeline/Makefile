APPSOURCES =  xf_stereo_pipeline_tb.cpp  xf_stereo_pipeline_accel.cpp
EXECUTABLE = stereo_pipeline.elf
VERBOSE = #-verbose
PLATFORM ?= <Path-to-reVISION-Platform>

URAM_ENA := 0

ifeq ($(URAM_ENA), 1)
   URAM_ENA_FLAG := -DXF_USE_URAM=true
else
   URAM_ENA_FLAG := -DXF_USE_URAM=false
endif

IDIRS = -I. -I${SYSROOT}/usr/include -I ../../include
LDIRS = --sysroot=${SYSROOT} -L=/lib -L=/usr/lib -Wl,-rpath-link=${SYSROOT}/lib,-rpath-link=${SYSROOT}/usr/lib

SDSCC_FLAGS = -sds-pf ${PLATFORM}  ${VERBOSE} ${URAM_ENA_FLAG} \
-sds-hw "xf::InitUndistortRectifyMapInverse<9, 5,4,1080,1920,1>"  xf_stereo_pipeline_accel.cpp -files ../../include/imgproc/xf_stereo_pipeline.hpp -clkid 2 -sds-end -dmclkid 2\
-sds-hw "xf::remap<128, 1,0,4,0,1080,1920,1,${URAM_ENA}>"  xf_stereo_pipeline_accel.cpp -files ../../include/imgproc/xf_remap.hpp -clkid 2 -sds-end -dmclkid 2\
-sds-hw "xf::StereoBM<15, 48,16,0,1,1080,1920,1,${URAM_ENA}>"  xf_stereo_pipeline_accel.cpp -files ../../include/imgproc/xf_stereoBM.hpp -clkid 2 -sds-end -dmclkid 2

CC = sds++ ${SDSCC_FLAGS}

LLIBS = -lopencv_imgcodecs -lopencv_core -llzma -ltiff -lpng16 -lz -ljpeg -lopencv_imgproc -ldl -lrt -lwebp -lopencv_features2d -lopencv_flann -lopencv_calib3d

CFLAGS =  -O0 -Wall ${OLEVEL} -c ${IDIRS} -DHLS_NO_XIL_FPO_LIB -mstrict-align -hls-target 1
LFLAGS = ${OLEVEL} ${LDIRS} ${LLIBS}

SOURCES := $(patsubst %,./%,$(APPSOURCES))
OBJECTS := $(APPSOURCES:.cpp=.o)
DEPS := $(OBJECTS:.o=.d)
.PHONY: all
all: ${EXECUTABLE}
${EXECUTABLE}: ${OBJECTS}
	${CC} ${OBJECTS} ${LFLAGS} -o $@
-include ${DEPS}
%.o: %.cpp
	${CC} ${CFLAGS} $< -o $@
clean:
	${RM} ${EXECUTABLE} ${OBJECTS}
ultraclean: clean
	${RM} -rf _sds sd_card ${EXECUTABLE}.bit ${EXECUTABLE} ${OBJECTS}
