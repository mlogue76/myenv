# ZSH 설치
```
sudo apt-get install zsh git curl wget
chsh -s `which zsh`
```
# Oh My Zsh 설치
```
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh # 또는
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
# Plug-in
## zsh-syntax-highlighting
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
## zsh-autosuggestions
```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
## .zshrc
```
plugins=(
   git
   zsh-syntax-highlighting
   zsh-autosuggestions
   fasd
   z
)
```
## fzf
### Clone fzf
```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```
### .zshrc
```
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
eval "$(fasd --init posix-alias zsh-hook)"
```
