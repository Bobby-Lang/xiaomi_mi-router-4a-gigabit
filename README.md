# xiaomi_mi-router-4a-gigabit
编译固件
通过互联网查找资料，收集整理，测试，让固件编译真实可用；
环境变量 (env)：
TARGET_PROFILE: 设置为你的路由器型号，这里是 MI-R4A。
PADAVAN_DIR, FIRMWARE_OUTPUT_DIR: 定义了源码和输出固件的路径，方便管理。
PADAVAN_REPO_URL: 指定了 Padavan 源码仓库
步骤分解：
1. Checkout Workflow Code: 获取你仓库中包含此 workflow 文件的代码。
2. Initialize Build Environment: 安装编译所需的各种依赖包。使用了 --no-install-recommends 减少不必要的包。
3. Clone Padavan Source Code: 从指定仓库克隆 Padavan 源码，只克隆最新版本 (--depth=1) 以节省时间。同时创建最终的固件输出目录并赋予权限。
4. Download Toolchain: 进入工具链目录并执行下载脚本。
5. Prepare Firmware Configuration:
复制模板配置文件。
使用 update_config_option 函数来修改 .config 文件。这个函数会先删除旧的配置行，再添加新的，确保配置的唯一性和正确性。你可以根据自己的需求，在这里修改 CONFIG_FIRMWARE_INCLUDE_XXX 的值 (y表示集成, n表示不集成)。
CPU超频部分请务必谨慎，确认频率值 0x3B2 (即十进制946，代表1200MHz) 对你的设备是安全的。
6. Build Firmware:
执行 clear_tree 清理上一次的编译。
执行核心的编译脚本 build_firmware_modify。
编译完成后，脚本会尝试在几个可能的位置查找生成的固件文件（优先检查是否已在最终输出目录，其次在 trunk/images/ 及其子目录中查找 .bin, .trx, .img 文件），并将其移动到 ${{ env.FIRMWARE_OUTPUT_DIR }}。
增加了更详细的日志输出和错误检查，如果找不到文件会尝试列出相关目录内容。
7. Upload Firmware Artifact: 将 ${{ env.FIRMWARE_OUTPUT_DIR }} 目录下的 .bin 文件（或其他你指定的固件文件）打包上传到 GitHub Actions 的 "Artifacts"，方便你下载。
8. Clean up build directory (可选): 编译完成后删除克隆的源码目录，以释放 GitHub Actions Runner 的磁盘空间。
