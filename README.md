# StarFab
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
## Install PyEnv
[more info](https://github.com/pyenv/pyenv)
```
curl https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec "$SHELL"
```

# Setup Git
```
mkdir -p scmodding-fedora/{tools,frameworks}/
cd scmodding-fedora
git clone https://gitlab.com/scmodding/tools/starfab.git tools/starfab
git clone https://gitlab.com/scmodding/frameworks/scdatatools.git frameworks/scdatatools
```

# Fixing VTK dependancy bug
[more info](https://gitlab.com/scmodding/tools/starfab/-/issues/70)
```
cd tools/starfab
rm poetry.lock
nano pyproject.toml # delete [[tool.poetry.dependencies.vtk]] entries
```

# Install specific python using pyenv
pyenv install 3.10.2
pyenv shell  
pyenv local 3.10.2
pyenv versions

# Setup python environment
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel vtk
poetry env use python


# Build StarFab
poetry install
#poetry run python -m pip install -e ../../frameworks/scdatatools
#pip install -e ../../frameworks/scdatatools

# Environment Variables
    #export QT_QPA_PLATFORM=wayland
    # Sway and Hyprland bug: https://bugreports.qt.io/browse/QTBUG-81504
export QT_QPA_PLATFORM=xcb
export WAYLAND_DEBUG=client
export QT_DEBUG_PLUGINS=1

# Run StarFab, after splash screen UI can take sometime to appear, keep eye on terminal to spot for errors
poetry run python -m starfab
