# Steps


## 1) Install some key dependencies
``` 
sudo apt install -y \
  build-essential \
  bzip2 \
  capnproto \
  curl \
  expect \
  git \
  libbz2-dev \
  libcapnp-dev \
  libffi-dev \
  liblzma-dev \
  libreadline-dev \
  libreadline8 \
  libsqlite3-dev \
  libssl-dev \
  python-tk \
  python3-tk \
  snap \
  sqlite3 \
  tk \
  tk-dev \
  xclip \
  zlib1g-dev
  
sudo apt update && sudo apt upgrade
```

## 2) If Gazelle from System76:
### 2.1) Install kernelstub and fix the post update/install scripts
```
sudo apt install -y git
git clone https://github.com/isantop/kernelstub
cd kernelstub
sudo apt install python3.10-distutils ## Assuming `python3 -V` is 3.10.6
sudo python3 setup.py install --record installed_files.txt
```

Check the `installed_files.txt`. There should be two files to be fixed:
- `/etc/initramfs/post-update.d/zz-kernelstub`
- `/etc/kernel/postinst.d/zz-kernelstub`

For each of these two files, edit them to include `sudo` before `kernelstub`.

### 2.2) Fix the jumping mouse issue
Issue: https://github.com/pop-os/gnome-shell/issues/124, https://github.com/pop-os/pop/issues/1572
```
modprobe -r psmouse
sudo crontab -e
```
Then insert this into cron:
```
@reboot modprobe -r psmouse
```

### 2.3) Install the required drivers
More info: https://support.system76.com/articles/system76-driver/
Run:
```
sudo apt-add-repository -y ppa:system76-dev/stable
sudo apt update && sudo apt upgrade
sudo apt install system76-driver system76-driver-nvidia
```

## 3) Install i3wm
```
sudo apt install i3
```
Now restart your machine and after putting up the username, change to i3.
Pull the config from here (TODO) and tweak the monitor part using `xrandr`.

## 4) Install apps

- Google Chrome: https://www.google.com/intl/en_uk/chrome/
  - Remember to enable sync to get all extensions for free
- NordLayer:
  - Download from here https://nordlayer.com/download/linux/
  - `sudo apt install ./Downloads/nordlayer-latest_1.0.0_all.deb`
  - `sudo apt update`
  - `sudo apt install nordlayer`
- Telegram: `sudo snap install telegram-desktop`
- Todoist: `sudo snap install todoist`
- Slack: `sudo snap install slack`
- VS Code:
  - Download from https://code.visualstudio.com/ and install with `sudo apt install ~/Downloads/code...`
  - Install Victor Mono font:
    - Download [here](https://rubjo.github.io/victor-mono/) and extract it
    - Run `sudo cp -r ~/Downloads/victormono /usr/local/share/fonts/victormono`
    - Run `sudo fc-cache -fv`
  - Turn on auto sync 
authenticate to the appropriate git 

## 5) Install fish

```
sudo apt-add-repository ppa:fish-shell/release-3 
sudo apt update && sudo apt upgrade
sudo apt install fish
sudo chsh -s /usr/local/bin/fish
reboot
```

## 6) Install Python

```
curl https://pyenv.run | bash
set -Ux PYENV_ROOT $HOME/.pyenv
fish_add_path $PYENV_ROOT/bin
echo "pyenv init - | source" >> ~/.config/fish/config.fish
pyenv install 3.11.1
pyenv global 3.11.1
```

### 6.1) Install poetry

```
curl -sSL https://install.python-poetry.org | python -
fish_add_path $home/.local/bin
```

## 7) Install Rust

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
fish_add_path $HOME/.cargo/bin
cargo install cargo-udeps --locked
```

## 8) Clone the repos & first install and build

First, setup git account:
```
type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt upgrade \
&& sudo apt install gh -y

gh auth login
```
