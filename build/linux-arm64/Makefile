include ../../VERSION

BASE_DIR := $(realpath ../..)

include ../env.mk

TARGET_OS ?= linux
TARGET_CPU ?= arm64

include ../common.mk

EXTRA_GN_ARGS := \
	target_os="$(TARGET_OS)" \
	target_cpu="$(TARGET_CPU)" \
	$(RTC_IS_DEBUG) \
	$(RTC_USE_H264) \
	$(RTC_USE_X11) \
	rtc_include_tests=false \
	rtc_build_tools=false \
	rtc_build_examples=false \
	rtc_use_pipewire=false \
	rtc_build_json=true \
	use_rtti=true \
	use_custom_libcxx=false \
	use_custom_libcxx_for_host=false \
	proprietary_codecs=true \
	ffmpeg_branding="Chrome"

.PHONY: all
all: clean build package

.PHONY: package
package: common-package

.PHONY: copy
copy: common-copy

.PHONY: patch
patch: common-patch
	cd $(SRC_DIR) && \
	patch -p2 < $(PATCH_DIR)/4k_linux.patch && \
	patch -p2 < $(PATCH_DIR)/disable_use_hermetic_xcode_on_linux.patch && \
	patch -p2 < $(PATCH_DIR)/linux_is_pod_deprecate.patch && \
	patch -p2 < $(PATCH_DIR)/linux_clang_optional.patch

.PHONY: build
build: download patch
	cd $(SRC_DIR) && \
	gn gen $(BUILD_DIR) --args='$(EXTRA_GN_ARGS) $(CC_WRAPPER)' && \
	ninja -C $(BUILD_DIR)

	cd $(BUILD_DIR)/obj && \
	/usr/bin/ar -rc $(BUILD_DIR)/obj/libwebrtc.a `find . -name '*.o'`

