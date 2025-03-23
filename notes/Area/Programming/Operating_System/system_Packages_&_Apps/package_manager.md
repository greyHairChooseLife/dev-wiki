# 󰏢 package manager



> [!lg] Log 2025-03-13
>
> 그동안 너무 모르고 해왔다. 패키지를 설치하고, 시스템 및 패키지를 업데이트하는 것은 너무나 간단했기
> 때문이다.
>
> 근데 내 랩탑이 너무 느려지고.. 뭔가 업데이트 할 때마다 너무 자주 보이는 패키지명은 '꼭 필요한가?'하고
> 생각하게끔 만들었다. orphaned packages나 as a dependency로 설치되었는가 여부를 표시할 수 있는걸로
> 알긴 안다만, 제대로 활용한 적이 없다.
>
> ```sh
> ## File Path: package_manager.sh
> # Install packages using yay (change to pacman/AUR helper of your choice)
> yayinstall() {
>   yay -Slq | fzf -q "$1" -m --preview 'yay -Si {1}' | xargs -ro yay -S
> }
> # Remove installed packages (change to pacman/AUR helper of your choice)
> yayremove() {
>   yay -Qq | fzf -q "$1" -m --preview 'yay -Qi {1}' | xargs -ro yay -Rns
> }
>
> # Install packages using yay (change to pacman/AUR helper of your choice)
> pacinstall() {
>   pacman -Slq | fzf -q "$1" -m --preview 'pacman -Si {1}' | xargs -ro sudo pacman -S
> }
> # Remove installed packages (change to pacman/AUR helper of your choice)
> pacremove() {
>   pacman -Qq | fzf -q "$1" -m --preview 'pacman -Qi {1}' | xargs -ro sudo pacman -Rns
> }
> ```


