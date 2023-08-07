# StarFab - Linux Compile Guide
Guide for compiling StarFab on Linux distos. Although not necassary, it is reccomended to use toolbox or podman container to avoid filling the host OS with hundred of dev packages.
## Create a container for whichever distro you favour
```
# Fedora
toolbox create --image fedoraproject.org/fedora-toolbox:38
toolbox enter fedora
sudo dnf -y update

# Debian
toolbox create --image quay.io/toolbx-images/debian-toolbox:12
toolbox enter debian-toolbox-12
sudo apt update && sudo apt upgrade -y


# Arch
toolbox create --image quay.io/toolbx-images/archlinux-toolbox
toolbox enter archlinux-toolbox
sudo pacman -Syu
```
## Get distro specific packages for compiling StarFab
```
#Fedora
sudo dnf -y groupinstall "Development Tools" "Development Libraries"
sudo dnf -y install poetry qt5-qtbase-devel libXcomposite libXdamage libXrandr libXcursor libXi libXtst alsa-lib pulseaudio-libs pulseaudio-libs-glib2
sudo dnf -y builddep python3

# Debian
sudo apt install -y build-essential python3-poetry qtbase5-dev
sudo apt install -y gdb lcov pkg-config libbz2-dev libffi-dev libgdbm-dev libgdbm-compat-dev liblzma-dev libncurses5-dev libreadline6-dev libsqlite3-dev libssl-dev lzma lzma-dev tk-dev uuid-dev zlib1g-dev pkg-config

# Uncomment deb-src from sources.list
sudo nano /etc/apt/sources.list
sudo apt build-dep python3

# Arch
sudo pacman -S pyenv qtbase tk poetry glu nss libxcomposite libxdamage libxrandr libxcursor libxi libxtst libxkbcommon alsa-lib libpulse
```
## Install PyEnv for bash shell
[more info](https://github.com/pyenv/pyenv)
```
curl https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec "$SHELL"
```
## Clone repositories
```
mkdir -p scmodding-fedora/{tools,frameworks}/
cd scmodding-fedora
git clone https://gitlab.com/scmodding/tools/starfab.git tools/starfab
git clone https://gitlab.com/scmodding/frameworks/scdatatools.git frameworks/scdatatools
```
## Fix VTK dependancy bug
[more info](https://gitlab.com/scmodding/tools/starfab/-/issues/70)
```
cd tools/starfab
rm poetry.lock

# use editor to remove [[tool.poetry.dependencies.vtk]] entries
nano pyproject.toml 
```
## Install recommended python using pyenv
```
pyenv install 3.10.2
pyenv shell 3.10.2
pyenv local 3.10.2
```
## Setup python environment
```
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel vtk

# make poetry recongnise selected pyenv python
poetry env use python
```
## (optional) Fix Qt bug for sway, hypr and other wayland compositors
[more info](https://bugreports.qt.io/browse/QTBUG-81504)
```
export QT_QPA_PLATFORM=xcb
```

## Build StarFab
```
poetry install
```
## Run StarFab
*note: after splash screen UI can take sometime to appear
```
poetry run python -m starfab
```
