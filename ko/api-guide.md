## AI Service > Face Liveness > API 가이드

### Face Liveness API

#### 요청
* {appKey}와 {secretKey}는 콘솔 상단 URL & Appkey 메뉴에서 확인이 가능합니다.

[URI]

| 메서드  | URI                                                                        |
|------|----------------------------------------------------------------------------|
| POST | https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing |

[요청 헤더]

| 이름            | 값           | 설명              |
|---------------|-------------|-----------------|
| Authorization | {secretKey} | 콘솔에서 발급 받은 보안 키 |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 앱키 또는 서비스 앱키 |

#### Content-Type: application/json

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 예제 | 설명 |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | 얼굴 감지에 사용할 이미지 |
| image.url | string | △ |  |  | "https://..." | 이미지의 URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64로 인코딩된 이미지 바이트 |
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | 스푸핑 감지 감도 |

* image.url, image.bytes 중 반드시 1개만 있어야 합니다.

#### Content-Type: multipart/form-data

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 예제 | 설명 |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  | | image.png | 얼굴 감지에 사용할 이미지 파일 | 
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | 스푸핑 감지 감도 |

<details>
<summary>요청 예</summary>

``` shell
curl -X POST 'https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing' \
-H 'Authorization: {secretKey}' \
-H 'Content-Type: application/json;charset=UTF-8' \
-d '{
    "image": {
        "url":"https://..."
    }
}'
```

``` shell
curl -X POST 'https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing' \
-H 'Authorization: {secretKey}' \
-H 'Content-Type: multipart/form-data' \
-F imageFile=@image.png
```

</details>

#### 응답

[응답 본문 헤더]

| 이름            | 타입      | 설명                               |
|---------------|---------|----------------------------------|
| header.isSuccessful  | Boolean | 분석 API 성공 여부                     |
| header.resultCode    | Integer | 결과 코드                            |
| header.resultMessage | String  | 결과 메시지(성공 시 Success, 실패 시 오류 내용) |

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | 감지한 얼굴 수 |
| data.faceDetails[].bbox | object | O | - | 이미지 내에서 감지한 얼굴의 경계 상자(bounding box) 정보 |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.faceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.faceDetails[].spoofing | boolean |  | false | 얼굴 스푸핑 여부 |
| data.faceDetails[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "faceDetailCount": 1,
        "faceDetails": [
            {
                "bbox": {
                    "x0": 0.08727272727272728,
                    "y0": 0.29333333333333333,
                    "x1": 0.24363636363636362,
                    "y1": 0.6213333333333333
                },
                "landmarks": [
                    {
                        "type": "leftEye",
                        "x": 0.1290909090909091,
                        "y": 0.4266666666666667
                    },
                    {
                        "type": "rightEye",
                        "x": 0.2018181818181818,
                        "y": 0.432
                    },
                    {
                        "type": "nose",
                        "x": 0.16545454545454547,
                        "y": 0.49333333333333335
                    },
                    {
                        "type": "leftLip",
                        "x": 0.13818181818181818,
                        "y": 0.5546666666666666
                    },
                    {
                        "type": "rightLip",
                        "x": 0.19272727272727272,
                        "y": 0.56
                    }
                ],
                "spoofing": true,
                "confidence": 0.997854
            }
        ]
    }
}
```
</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| ---------- | ------------- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41005 | UnauthorizedAppKeyOrSecretKey | 승인되지 않은 Appkey 또는 SecretKey |
| -45020 | ImageTooLargeException | 이미지 크기 초과 |
| -45030 | InvalidImageParameterException | 잘못된 이미지 파라미터. 주로 Base64 인코딩이 잘못된 경우 발생 |
| -45040 | InvalidImageFormatException | 지원하지 않는 이미지 포맷 |
| -45050 | InvalidImageURLException | 잘못된 이미지 URL |
| -45060 | ImageTimeoutError | 이미지 다운로드 시간 초과 |
| -50000 | InternalServerError | 서버 오류 |