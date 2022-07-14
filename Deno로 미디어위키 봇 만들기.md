# Deno로 미디어위키 봇 만들기
## 왜?
처음엔 그냥 있는 라이브러리 불러와서 쓰려고 했는데 skypack, esm.sh도 잘 안먹히고 node 호환모드로도 잘 안되길래 그냥 새 라이브러리를 만들었다.
https://github.com/gnlow/deno-mw

## 우여곡절
그냥 API 래퍼 구현하는거라 간단할 줄 알았는데 엄청 고생했다...
### NeedToken
가장 기본적인 Login 기능을 구현하는데 NeedToken 오류가 자꾸 났다.
처음엔 쿠키 문제일거라 생각해서 사용중인 쿠키 라이브러리(another_cookiejar)가 잘 되는지 테스트하는데 시간을 많이 썼다.
결론적으로 라이브러리는 아무 죄가 없었고... `JSON` 형식으로 `POST`를 보낸게 문제였다.
https://www.mediawiki.org/wiki/Topic:Vmwf9vs5t9118w5m
`application/x-www-form-urlencoded`을 이용해 해결.
```ts
async POST(params: ParamMap) {
    const {
        action,
        ...rest
    } = params
    return await this.fetch(
        urlcat(this.apiUrl, {action, format: "json"}),
        {
            method: "POST",
            body: urlcat("", rest),
            headers: {
                "user-agent": this.userAgent,
                "content-type": "application/x-www-form-urlencoded",
            }
        }
    )
}
```
### File upload parameter "file" is not a file upload; be sure to use multipart/form-data for your POST
파일 업로드 기능을 구현하는데 발생한 오류.
stackoverflow를 뒤져서 body를 FormData로 보내야한다는건 찾았는데,
그렇게 해도 똑같은 오류가 계속 나는거다..
```ts
async POST_multipart(params: ParamMap) {
    const {
        action,
        ...rest
    } = params
    const formData = new FormData()
    Object.entries(rest).forEach(
        ([name, value]) => formData.append(name, value)
    )
    console.log(formData)

    return await this.fetch(
        urlcat(this.apiUrl, {action, format: "json"}),
        {
            method: "POST",
            body: formData,
            headers: {
                "user-agent": this.userAgent,
                //"content-type": "multipart/form-data",
            }
        }
    )
}
```
해결책은 어이없게도 content-type 헤더를 지우는거였다.
https://stackoverflow.com/questions/48284011/how-to-post-image-with-fetch
