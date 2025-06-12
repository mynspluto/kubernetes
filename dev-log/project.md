## ide 설치

jetbrain goland tar 다운로드
압축 해제
GoLand-2015.1.2/bin/goland.sh 우클릭 실행하여 설치
sudo ln -s ~/GoLand-2024.1.2/bin/goland.sh /usr/local/bin/goland
sudo vi ~/.local/share/applications/goland.desktop

```sh
[Desktop Entry]
Version=1.0
Type=Application
Name=GoLand
Exec=/home/mynspluto/GoLand-2025.1.2/bin/goland.sh
Icon=/home/mynspluto/GoLand-2025.1.2/bin/goland.png
Comment=JetBrains Go IDE
Categories=Development;IDE;
Terminal=false

```

sudo chmod +x ~/.local/share/applications/goland.desktop
