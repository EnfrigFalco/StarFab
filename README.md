# StarFab - Linux Compile Guide
Guide for compiling StarFab on Linux distos. Although not necassary, it is reccomended to use toolbox or podman container to avoid filling the host OS with hundred of dev packages.
(optional) ## Create a container for whichever distro you favour
```
# Arch
toolbox create --image quay.io/toolbx-images/archlinux-toolbox
toolbox enter archlinux-toolbox
sudo pacman -Syu

# Debian
toolbox create --image quay.io/toolbx-images/debian-toolbox:12
toolbox enter debian-toolbox-12
sudo apt update && sudo apt upgrade -y

# Fedora
toolbox create --image fedoraproject.org/fedora-toolbox:38
toolbox enter fedora
sudo dnf -y update
```


## 1. Get distro specific packages for compiling StarFab
```
# Arch
sudo pacman -S  --needed base-devel poetry qtbase pyenv openssl zlib xz tk

libxcomposite libxdamage libxrandr libxcursor libxi libxtst libxkbcommon alsa-lib libpulse

# Debian
sudo apt install -y build-essential python3-poetry qtbase5-dev libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev curl libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev libasound2 libpulse-mainloop-glib0

#Fedora
sudo dnf -y groupinstall "Development Tools" "Development Libraries"
sudo dnf -y install poetry qt5-qtbase-devel libXcomposite libXdamage libXrandr libXcursor libXi libXtst alsa-lib pulseaudio-libs pulseaudio-libs-glib2
sudo dnf -y builddep python3
```
## 2. Install PyEnv for bash shell
[more info](https://github.com/pyenv/pyenv)
```
curl https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> /etc/apt/sources.list.d/debian.sources
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec "$SHELL"
```
## 3. Clone repositories
```
mkdir -p scmodding/{tools,frameworks}/
cd scmodding
git clone https://gitlab.com/scmodding/tools/starfab.git tools/starfab
git clone https://gitlab.com/scmodding/frameworks/scdatatools.git frameworks/scdatatools
```
## 4. Fix VTK dependancy bug
[more info](https://gitlab.com/scmodding/tools/starfab/-/issues/70)
```
cd tools/starfab
rm poetry.lock

# use editor to remove [[tool.poetry.dependencies.vtk]] entries
nano pyproject.toml 
```
## 5. Install recommended python using pyenv
```
pyenv install 3.10.2
pyenv shell 3.10.2
pyenv local 3.10.2
```
## 6. Setup python environment
```
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel vtk

# make poetry recongnise selected pyenv python
poetry env use python
```
## (optional) Qt wayland bug workaround on sway, hypr, and other wayland compositors
[more info](https://bugreports.qt.io/browse/QTBUG-81504)
```
export QT_QPA_PLATFORM=xcb
```

## 7. Build StarFab
```
poetry install
```
## 8. Run StarFab
*note: after splash screen UI can take sometime to appear
```
poetry run python -m starfab
```
