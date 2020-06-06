1.下载ndk-r14b

2.下载ffmpeg-3.3.9

3.解压ffmpeg-3.3.9,跳转到ffmpeg根目录，修改configure文件：

    修改前
    SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'
    LIB_INSTALL_EXTRA_CMD='$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"'
    SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'
    SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR) $(SLIBNAME)' 
    
    修改后
    SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'
    LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'
    SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'
    SLIB_INSTALL_LINKS='$(SLIBNAME)'
4.创建build_android.sh文件
  
    #!/bin/sh
    #--disable-avdevice
    NDK=/Users/apple/android-ndk-r14b
    export PATH=$PATH:$NDK
    SYSROOT=$NDK/platforms/android-21/arch-arm
    TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
    export RANLIB=$TOOLCHAIN/bin/arm-linux-androideabi-ranlib
    function build_one
    {
    ./configure \
    --prefix=$PREFIX \
    --enable-shared \
    --disable-static \
    --disable-doc \
    --disable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --disable-ffserver \
    --disable-doc \
    --disable-symver \
    --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
    --target-os=linux \
    --arch=arm \
    --enable-cross-compile \
    --sysroot=$SYSROOT \
    --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS" \
    $ADDITIONAL_CONFIGURE_FLAG
    make clean
    make -j4
    make install
    }
    CPU=armv7-a
    PREFIX=$(pwd)/android/$CPU
    ADDI_CFLAGS="-marm"
    build_one

5.chmod a+x build_android.sh修改文件为可执行，./build_android.sh执行文件开始编译
