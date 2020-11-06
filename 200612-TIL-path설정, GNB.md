## GNB, LNB, SNB, FNB

- GNB (Global Navigation Bar)
- LNB (Local Navigation Bar)
- SNB (Side Navigation Bar)
- FNB (Footer Navigation Bar)



## tsconfig.json paths 설정하기

```json
{
  "paths": {
    "@app/*": ["src/app/*"],
    "@environments/*": ["src/environments/*"],
    "@admin/*": ["projects/admin/src/app/*"],
    "@adminEnvironments/*": ["projects/admin/src/environments/*"]
  }
}
```

path를 축약해서 `../../../src` 이 꼴로 안 쓸 수 있다. 적용 초반에는 웹스톰이 잘 인식을 못 해서 두 세 번 정도 웹스톰을 껐다 켰더니 적용이 잘 된다.

