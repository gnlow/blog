# CSS로 홀로그램 만들기
![](https://github.com/gnlow/blog/blob/master/hologram_screenshot.png)  
CSS 홀로그램(Iridescence?) 효과가 필요해서 구글링 해보았지만 적당한 예제가 없어서 Mask를 이용해 직접 만들어 보았다.
```svelte
<style>
    pos {
        position: relative;
        width: 100%;
        height: 100%;
        display: block;
    }
    mas {
        width: 100%;
        height: 100%;
        mask: url("/img/staff.svg");
        mask-size: cover;
        display: block;
    }
    bg {
        position: fixed;
        top: 0;
        width: 100%;
        height: 100%;
        background: repeating-linear-gradient(to right bottom, #a2dbec, #f2e6ac 5%, #f1b2dd 10%, #949dc4 15%, #c0f3e2 20%, #a2dbec 25%)
    }
</style>

<pos>
    <mas>
        <bg/>
    </mas>
</pos>
```
그라데이션 배경의 div에 원하는 모양의 마스크를 씌우는 형태다. 그라데이션은 고정되어있고, 마스크는 스크롤에 따라 움직인다.  
조잡하지만 그래도 꽤 홀로그램 느낌이 난다.
## 참고한 자료
- CSS Mask 예제: https://marshall-ku.com/web/tips/css-mask-이용하기
- 광택 효과/색상: https://www.youtube.com/watch?v=-SJN4QQtprE
