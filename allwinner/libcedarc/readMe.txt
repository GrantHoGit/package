
һ��linux���˵��:

1. ÿ��linux so��Ӧ�ı��빤�������£�

so_dir: arm926-uclibc                 host: arm-linux              toolchain_dir: uclibc_arm926_static
so_dir: arm-linux-gnueabi             host: arm-linux-gnueabi      toolchain_dir: external-toolchain
so_dir: arm-linux-gnueabihf           host: arm-linux-gnueabihf    toolchain_dir: ϵͳ�Դ�
so_dir: arm-none-linux-gnueabi        host: arm-none-linux-gnueabi toolchain_dir: ϵͳ�Դ�
so_dir: arm-openwrt-linux-muslgnueabi host: arm-openwrt-linux      toolchain_dir: OpenWrt-Toolchain-sunxi_gcc-5.2.0_musl-1.1.12_eabi.Linux-x86_64
so_dir: arm-openwrt-linux-uclibc      host: arm-openwrt-linux      toolchain_dir: toolchain_arm_uClibc
so_dir: arm-linux-gnueabihf-linaro    host: arm-linux-gnueabihf    toolchain_dir: gcc-linaro-arm-linux-gnueabihf-4.8-2014.01_linux
so_dir: arm-aarch64-openwrt-linux     host: aarch64-openwrt-linux  toolchain_dir: toolchain-sunxi-tina2.0-64
so_dir: arm-openwrt-linux-muslgnueabi-v5  host: arm-openwrt-linux-muslgnueabi    toolchain_dir: linux-x86

2.���벽�����£���arm926-uclibcΪ������

2.1 export���빤������
                     TOOLS_CHAIN=/home/user/workspace/tools_chain/
                     export PATH=${TOOLS_CHAIN}/uclibc_arm926_static/bin:$PATH

2.2. ����automake����ع��ߣ�./bootstrap

2.3. ����makefile��
2.3.1 ģʽ��./configure --prefix=INSTALL_PATH --host=HOST_NAME LDFLAGS="-LSO_PATH"
2.3.2 ʾ����./configure --prefix=/home/user/workspace/libcedarc/install --host=arm-linux LDFLAGS="-L/home/user/workspace/libcedarc/lib/arm926-uclibc"
2.3.3 �ر�˵��������ں��õ���linux3.10, ��������flag��CFLAGS="-DCONF_KERNEL_VERSION_3_10" CPPFLAGS="-DCONF_KERNEL_VERSION_3_10"
	  ����./configure --prefix=/home/user/workspace/libcedarc/install --host=arm-linux CFLAGS="-DCONF_KERNEL_VERSION_3_10" CPPFLAGS="-DCONF_KERNEL_VERSION_3_10" LDFLAGS="-L/home/user/workspace/libcedarc/lib/arm926-uclibc"

2.4 ���룺make ; make install

����android���˵����

1). CedarC-v1.0.4

1. �ر�˵����

1.1 cameraģ����أ�( >=android7.0��ƽ̨��Ҫ���������޸�,����ƽ̨����֮ǰ������)
    �޸ĵ�ԭ��a.omx��android framework��û�ж�NV21��NV12������ͼ���ʽ����ϸ�֣�������OMX_COLOR_FormatYUV420SemiPlanar
	            ���б�ʾ��OMX_COLOR_FormatYUV420SemiPlanar�����Ա�ʾNV21��Ҳ���Ա�ʾNV12��
                b.��ACodec��OMX_COLOR_FormatYUV420SemiPlanar����NV12��camera��OMX_COLOR_FormatYUV420SemiPlanar����NV21��
                ֮ǰ��������omx_vencͨ�����̵İ����������ּ��ݣ�����������ACodec��������NV12������������camera��������
				NV21��
				c.android7.0��ΪȨ�޹����ԭ���޷���ȡ�����̵İ����������޷���omx_venc���м��ݣ�ֻ�����ϲ�caller�����
				���ݣ���cts���ACodec�Ľӿڽ��в��ԣ����Ķ�ACodec�������Ӧ��cts���ԣ�����ֻ���޸�camera
				d.�޸ĵ�ԭ��Ϊ����չͼ���ʽ��ö�����͵ĳ�Ա����OMX_COLOR_FormatYVU420SemiPlanar�����ڱ�ʾNV21��
				  �� OMX_COLOR_FormatYUV420SemiPlanar --> NV12
				     OMX_COLOR_FormatYVU420SemiPlanar --> NV21

	�޸ĵĵط���
	a. ͬ��ͷ�ļ���ͬ��openmax/omxcore/inc/OMX_IVCommon.h ��./native/include/media/openmax/OMX_IVCommon.h
	b. cameraģ���ڵ���openmax/venc����ӿ�ʱ��Ҫ�����޸ģ�
	   �޸�ǰ NV21 --> OMX_COLOR_FormatYUV420SemiPlanar��
	   �޸ĺ� NV21 --> OMX_COLOR_FormatYVU420SemiPlanar;

2. �Ķ������£�
2.1 openmax:venc add p_skip interface
2.2 h265:fix the HevcDecodeNalSps and HevcInitialFBM
2.3 h264:refactor the H264ComputeOffset
2.4 mjpeg scale+rotate������
2.5 vdcoder/h265: add the code of parser HDR info
2.6 vdecoder/h265: add the process of error-frame
2.7 vdecoder/h264: make sure pMbNeighborInfoBuf is 16K-align to fix mbaff function
2.8 openmax/vdec: remove cts-flag
2.9 openmax/venc: remove cts-flag
2.10 detection a complete frame bitstream and crop the stuff zero data
2.11 vdecoder/h265:fix the bug that the pts of keyFrame is error when seek
2.12 openmax/inc: adjust the define of struct
2.13 vencoder: add lock for vencoderOpen()
2.14 vdecoder/ALMOST decoders:fix rotate and scaledown
2.15 vdecoder/h265:fix the process of searching the start-code when sbm cycles
2.16 vdecoder/h265:fix the bug when request fbm fail after fbm initial
2.17 vdecoder/h265:improve the process when poc is abnormal
2.18 cedarc: unify the release of android and linux
2.19 vdecoder/avs: make mbInfoBuf to 16K align

2).CedarC-v1.0.5

1. �Ķ������£�
1.1.configure.ac:fix the config for linux compiling
1.2.openmax/venc: revert mIsFromCts on less than android_7.0 platfrom
1.3.vdecoder/h265soft:make h265soft be compatible with AndroidN
1.4.cedarc: merge the submit of cedarc-release
1.5.vdecoder/h265:use the flag "bSkipBFrameIfDelay"
1.6.vdecoder:fix the buffer size for thumbnail mode
1.7.cedarc: fix compile error of linux
1.8.omx:venc add fence for androidN
1.9.openmax:fix some erros
1.10.omx_venc: add yvu420sp for omx_venc init
1.11.videoengine:add 2k limit for h2
1.12.cedarc: add the toolschain of arm-openwrt-linux-muslgnueabi for v5
1.13.cedarc: ���0x1663 mpeg4 ���Ż���������
1.14.vdecoder: fix compile error of soft decoder for A83t
1.15.omx_vdec: fix the -1 bug for cts
1.16.�޸�mpeg2 ��ȡve version�ķ�ʽ
1.17.vencoder: fix for input addrPhy check
1.18.cedarc: merge the submit of cedarc-release