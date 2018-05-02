如果安装的是中文版Ubuntu，那么/home下的目录会是“桌面”“下载”等，在终端下进入这些目录看起来很不爽，那怎样改为英文目录呢，很简单：

STEP1: 将这些目录修改为英文名，如：  mv 桌面 Desktop 

STEP2: 修改配置文件  ～/.config/user-dirs.dirs ，将对应的路径改为英文名（要和STEP1中修改的英文名对应）

```
vim ~/.config/user-dirs.dirs
```

配置文件修改后的内容如下：

```
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Download"
XDG_TEMPLATES_DIR="$HOME/Template"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Document"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Picture"
XDG_VIDEOS_DIR="$HOME/Video"
```