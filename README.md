# ffmpeg_qsv_cuda

windows:

1.Install msys2 from www.msys2.org.

2.Clone ffnvcodec

  git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git
  
3. Clone FFmpeg's public GIT repository.

git clone https://git.ffmpeg.org/ffmpeg.git

4.Create a folder named nv_sdk in the parent directory of FFmpeg and copy all the
header files from C:\Program Files\NVIDIA GPU Computing

Toolkit\CUDA\v8.0\include and library files from C:\Program Files\NVIDIA GPU
Computing Toolkit\CUDA\v8.0\lib\x64 to nv_sdk folder.

5.Launch the Visual Studio x64 Native Tools Command Prompt.

6. From the Visual Studio x64 Native Tools Command Prompt, launch the MinGW64
environment by running mingw64.exe from the msys2 installation folder.

7. In the MinGW64 environment, install the necessary packages.

pacman -S diffutils make pkg-config yasm

8. Add the following paths by running the commands.

export PATH="/c/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v10.1/bin":$PATH

 export PATH="/c/Program Files (x86)/Microsoft Visual Studio/2019/Preview/VC/Tools/MSVC/14.20.27607/bin/Hostx64/x64":$PATH
 
export PATH="/c/Program Files (x86)/IntelSWTools/Intel(R) Media SDK 2018 R2/Software Development Kit/bin/x64":$PATH

9. Goto nv-codec-headers directory and install ffnvcodec

make install PREFIX=/usr

10.
 //export PKG_CONFIG_PATH=/z/ffmpeg_project/mfx_dispatch/build/lib/pkgconfig:$PKG_CONFIG_PATH
 //unset PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
./configure --enable-nonfree --disable-shared  --enable-cuda-nvcc  --toolchain=msvc --enable-libnpp  --enable-libmfx --arch=x86_64  --extra-cflags="-I../nv_sdk/include -I../intel_sdk/include" --extra-ldflags="-libpath:../nv_sdk/lib/x64 -libpath:../intel_sdk/lib/x64" --prefix=./build --enable-gpl

11.make -j4

qsv:

ffmpeg.exe -rtbufsize 1G -init_hw_device qsv=hw -filter_hw_device hw  -f dshow  -i video="TBS 6314 HDMI Video Input0"  -video_device_number 0 -vf 'hwupload=extra_hw_frames=64,format=qsv,scale_qsv=w=1280:h=720,vpp_qsv=deinterlace=2:framerate=30' -c:v h264_qsv  -f rtp_mpegts rtp://127.0.0.1:4567

cuda:

ffmpeg.exe -y -vsync 0 -init_hw_device cuda=foo:bar -filter_hw_device foo -f dshow  -i video="TBS 6314 HDMI Video Input0" -vf 'scale_cuda=1280:720' -c:a copy -c:v h264_nvenc -b:v 5M -f rtp_mpegts rtp://127.0.0.1:4567
