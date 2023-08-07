# StarFab - Linux Guide
Guide for running StarFab on Linux distos using toolbox (podman) container to avoid flooding OS with hundred of packages.
## Setup a container
```
toolbox create fedora
toolbox enter fedora
sudo dnf -y update
```
## Get basic dev tools,libraries, python build dependancies, extra packages for compiling StarFab, and a kitchen sink
```
sudo dnf -y groupinstall "Development Tools" "Development Libraries" 
sudo dnf -y builddep python3
sudo dnf -y install poetry qt5-qtbase-devel libXcomposite libXdamage libXrandr libXcursor libXi libXtst alsa-lib pulseaudio-libs pulseaudio-libs-glib2
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
# check current python version with
pyenv versions
```
## Setup python environment
```
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel vtk
# make poetry recongnise pyenv python
poetry env use python
```
## Fix Qt bug for sway, hypr and other wayland compositors
[more info](https://bugreports.qt.io/browse/QTBUG-81504)
```
export QT_QPA_PLATFORM=xcb
```

## Build StarFab
```
poetry install
#poetry run python -m pip install -e ../../frameworks/scdatatools
#pip install -e ../../frameworks/scdatatools
```
## Run StarFab
*note: after splash screen UI can take sometime to appear
```
poetry run python -m starfab
```
