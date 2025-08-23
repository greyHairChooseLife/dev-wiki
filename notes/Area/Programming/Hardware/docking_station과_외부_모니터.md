# 󰏢 docking station과 외부 모니터




## docking station과 외부모니터 활용하기



> [!td]2025-08-23
> - [-] 자동화된 스크립트를 사용할 수 있다.
>  󱞪 



### dock에 노트북 장착

- 장치 인식하고 xrandr로 모니터 출력 설정

  ex)
  ```bash
  xrandr --output eDP-1 --auto \
    --output DP-2-2 --auto --right-of eDP-1 \
    --output DP-2-3 --auto --right-of DP-2-2
  ```

  _잘 안되면 `mons -o`로 초기화 한 뒤에 시도하면 잘 된다._


### dock에서 노트북 탈착 

- `mons -o` 실행

  _만약 `mons -o`를 실행하지 않으면 다시 dock에 장착했을 때 반응을 안한다.
  게다가 xrandr로 모니터 출력을 설정해도 무시한다._
